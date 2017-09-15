---
title: "Never Give Up, Retry: How Software Should Deal with Failures"
layout: post
date: 2017-09-15
location: Austin, Texas
description:
 The services that your software uses are never 100% available. Let's look how to deal with that.
comments: true
tags: [reliability, http, python, distributed]
og_image: /images/never-give-up-retry.png
keywords:
  - handling failures of http requests
  - retry pattern in python
  - reliable backend software
---

<div class="image-wrapper">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/never-give-up-retry.png"
      alt="Never Give Up, Retry: How Backend Software Should Deal with Failures"
      class="image-right"
      width="256"
      height="320"
      layout="fixed">
  </amp-img>
</div>

It's doubtful that your backend has everything within one process: you need to read configuration, store customers' data, write logs and metrics about the status of your software.

If you're working on a network application - it's even more complicated: your database can be far far away from the running code.

Some things can go wrong: a network blip might happen, the remote database can be overloaded by incoming requests, a query can reveal some bug in the DBMS and crash it, your data can be out of order on that side because of some reason, and so on.

Microservice architecture encourages cross-process communications over the network. Now your service asks another one for its configuration, that is stored somewhere in the database. You should prepare the software to non-deterministic failures which might occur during the data transfer. And not only then.

In the blog post, we will look into some common failures which can be solved with proper retrying. The basic ideas are described using Python, but experience with the language is not required for understanding.

<!--more-->

----

Failures When Retry Might Help
-----

Retry helps in the places when our code acts as a client of some other backend. It can be a wrapper for a database client, a client for an HTTP server, etc. As a consumer, we expect (sometimes without any reason) that the problem which prevents successful processing can be fixed by someone else shortly.

I can name two categories of problems when I look for that:
* errors during data transfer
* failures of processing because of load issues

In **the first one**, our request even does not reach the eventual endpoint - application code which handles your query on the other side. Possible reasons from my experience can be various:

* `DNSError` - domain name cannot be resolved into IP. It does not always mean that the system is not available. It might happen when your destination is being redeployed.
* `ConnectionError` - we failed to perform a connection handshake successfully. The connection might be not stable on the network level at the moment of making the request.
* `Timeout` - a server fails to send a byte after the specified timeout, but the connection was previously established. Some previous requests can be even processed successfully using the instance of your `HTTPClient`. The error may occur when the infrastructure of your destination scaled down, and the particular server which you used does not exist now. But service is available. You need to recreate `HTTPClient` and try to establish a new connection.

Some common database issue from the same group:
* `OperationalError` - the errors occur when connection to storage is lost or cannot be established at the moment. I think that it worth to recreate an instance of the client and try to connect again in such cases. You can see the examples of error codes returned by PostgreSQL:
```
Class 08 — Connection Exception
08000	connection_exception
08003	connection_does_not_exist
08006	connection_failure
08001	sqlclient_unable_to_establish_sqlconnection
08004	sqlserver_rejected_establishment_of_sqlconnection
```
* `ProtocolError` is an example from Redis. The exception is raised when Redis server received a sequence of bytes which is translated to a meaningless operation. Since you test your software before deploying that, it's unlikely that the error occurs because of poorly written code. Let's blame our transport layer :).

Thinking about the 2nd category of failures aka **load issues** I'd like to review the following responses from HTTP server:
* `408 Request Timeout` is returned when a server spent more time processing your request than it was prepared to wait. Possible reason: a resource is overwhelmed by a lot of incoming requests. Waiting and retrying after some delay can be a good strategy to finish data processing on your side eventually.
* `429 Too Many Requests` means that you sent more requests than the server allows you during some time frame. This technique which is used by the server is also known as rate-limiting. A good thing, that a server SHOULD return `Retry-After` header which provides a recommendation how long you need to wait before making the next request.
* `500 Internal Server Error`. That's the most infamous HTTP server error. The diversity of reasons for the error depends only on the good faith of the developers. For all uncaught exception occurred there the response is returned. I do not have a strong opinion that we should continuously retry on such errors. For each service which you use you should learn what's the reason behind the response.
> For the developers of web servers which are reading the lines, I suggest preventing sending of the type of response if possible. Think about using more specific response when you know the reason of failure.
* `503 Service Unavailable` - service currently cannot handle the request because of *temporary* overload. You can expect that it will be alleviated after some delay. The server CAN send `Retry-After` header like it was mentioned for `429 Too Many Requests` status code.
* `504 Gateway Timeout` is similar to `408 Request Timeout` but means that connection with your HTTP client was closed by the reverse-proxy which stands in front of the server.

But, hopefully, we do not live in the world where everything is transmitted over HTTP protocol. I want to share my experience of retrying on database errors:

* `OperationalError`. Yes, we already met this guy in the blog post. Both for PostgreSQL and MySQL it additionally covers the failures which are not under control of a software engineer. Examples: a memory allocation error occurred during processing, or a transaction could not be processed. I recommend retrying on them.
* `IntegrityError` - this is a tricky one. It can be raised when a foreign key constraint is violated, like when you try to insert a `Record A` which depends on `Record B`. And `Record B` might be not added yet because of asynchronous nature of your system. In this case, I'd retry. From another side, the exception is also raised when your attempt to add a record leads to duplication of the unique key. It's unlikely that we want to retry that time. You might ask me how to distinguish such cases and retry when it's needed. Hopefully, your DBMS returns code of the error. And your SQL driver already knows how to map them between exception classes. Here's the example for MySQL:

~~~~~~
# from pymysql.err
_map_error(IntegrityError, ER.DUP_ENTRY, ER.NO_REFERENCED_ROW,
           ER.NO_REFERENCED_ROW_2, ER.ROW_IS_REFERENCED,
           ER.ROW_IS_REFERENCED_2, ER.CANNOT_ADD_FOREIGN,
           ER.BAD_NULL_ERROR)

# from pymysql.constants.ER
BAD_NULL_ERROR = 1048
DUP_ENTRY = 1062
NO_REFERENCED_ROW = 1216
ROW_IS_REFERENCED = 1217
ROW_IS_REFERENCED_2 = 1451
NO_REFERENCED_ROW_2 = 1452
CANNOT_ADD_FOREIGN = 1215
~~~~~~
{: .language-python}

Knowing that information you can make your software to do not retry on `IntegrityError` only when it's `DUP_ENTRY` and retry for other reasonable cases.
References:
* [constants for MySQL errors](https://github.com/PyMySQL/PyMySQL/blob/master/pymysql/constants/ER.py){:target="_blank"}
* [the mapping between exception types in PyMYSQL and error codes](https://github.com/PyMySQL/PyMySQL/blob/master/pymysql/err.py#L77){:target="_blank"}



Naive Implementation of Retry Decorator
----

I can think about retrying only for I/O operations. Starting 2014 we tend to make production software that performs such operations in the asynchronous fashion. Twisted and asyncio have been our friends all the time.

Python has the expressive concept of decorators: syntax for adding new functionality to existing functions using capabilities of [High-order programming](https://en.wikipedia.org/wiki/Higher-order_programming){:target="_blank"}.

For example, we have the `fetch` function to make an asynchronous HTTP-request and download a page for `python.org`:

~~~~~~
# Example is taken from http://aiohttp.readthedocs.io/en/stable/#getting-started
import aiohttp
import asyncio

async def fetch(session, url):
    async with session.get(url) as response:
        return await response.text()

# Client code, provided for reference
async def main():
    async with aiohttp.ClientSession() as session:
        html = await fetch(session, 'http://python.org')
        print(html)

loop = asyncio.get_event_loop()
loop.run_until_complete(main())
~~~~~~
{: .language-python}

The `fetch` function works fine, but it might be not enough reliable. It's not protected from all the HTTP errors listed above. But we can make it better:

~~~~~~
@retry(aiohttp.DisconnectedError, aiohttp.ClientError,
       aiohttp.HttpProcessingError)
async def fetch(session, url):
    async with session.get(url) as response:
        return await response.text()
~~~~~~
{: .language-python}

Now the function does not give up when the specified exceptions occur. It tries to perform a request several times. This easy trick can make your software more reliable and remove various sliding bugs. Let's look how the naive implementation works under the hood:

~~~~~~
import logging

from functools import wraps

log = logging.getLogger(__name__)

def retry(*exceptions, retries=3, cooldown=1, verbose=True):
    """Decorate an async function to execute it a few times before giving up.
    Hopes that problem is resolved by another side shortly.

    Args:
        exceptions (Tuple[Exception]) : The exceptions expected during function execution
        retries (int): Number of retries of function execution.
        cooldown (int): Seconds to wait before retry.
        verbose (bool): Specifies if we should log about not successful attempts.
    """

    def wrap(func):
        @wraps(func)
        async def inner(*args, **kwargs):
            retries_count = 0

            while True:
                try:
                    result = await func(*args, **kwargs)
                except exceptions as err:
                    retries_count += 1
                    message = "Exception during {} execution. " \
                              "{} of {} retries attempted".
                              format(func, retries_count, retries)

                    if retries_count > retries:
                        verbose and log.exception(message)
                        raise RetryExhaustedError(
                            func.__qualname__, args, kwargs) from err
                    else:
                        verbose and log.warning(message)

                    if cooldown:
                        await asyncio.sleep(cooldown)
                else:
                    return result
        return inner
    return wrap
~~~~~~
{: .language-python}

As you can see, the basic idea is to catch expected exceptions until we reach the limit for the number of `retries`. Between each execution, we wait `cooldown` seconds. Also, we write logs about each failed attempt if we want to be verbose.

> Implementing something like that with your favorite programming language can be a good exercise. Especially, when the language does not have the concept support of higher-order functions. Or decorators.

Production-grade Solutions
----

In the example above we have a minimum number of settings to configure:
* types of exceptions to retry
* number of attempts
* the time between attempts
* verbosity for logging unsuccessful attempts

Sometimes it's enough. But I know cases when you need more features. Pick the ones that look sexy for you from the list of possible capabilities:
* retry on synchronous functions
* stopping after some timeout, regardless of the number of attempts
* wait random time within some boundaries between retries
* exponential backoff sleeping between attempts
* specifying additional attributes for exceptions to retry, like integer error codes (you remember the example about `IntegrityError`, right?)
* providing hooks before and after attempts
* retrying not on exceptions, but on some values which satisfy a predicate
* usage of some synchronization primitive to limit the number of ongoing requests against some backend
* reading configuration for retry logic from some other source dynamically like from Feature Flags as a Service
* retrying forever (sounds crazy for me, but who knows about your case)

If you're looking for a retry library for adding into your Python-based product you might be interested in this third-party projects:
* [tenacity](https://github.com/jd/tenacity){:target="_blank"}
* [retrying](https://github.com/rholder/retrying){:target="_blank"}
* [backoff](https://github.com/litl/backoff){:target="_blank"}
* [riprova](https://github.com/h2non/riprova){:target="_blank"}

Not using Python on your backend? At least JavaScript, Go and Java have [open-sourced implementations](https://github.com/search?utf8=%E2%9C%93&q=topic%3Aretry&type=Repositories){:target="_blank"} of retry helpers.

Summary
----

The product that you build does not depend only on the software which you write. You need to rely on external resources like databases or other services which perform some good things for your customers.
> Backend programming is about making a wrapper of calls to the software built by somebody else.

From my experience, I/O operations are the most vulnerable places for all kinds of random failures. In the blog post, I shared with you my recommendations when and why we should retry. But I would like to know:

**How do you decide when to retry?**

**What do you use to provide such functionality?**

-----
