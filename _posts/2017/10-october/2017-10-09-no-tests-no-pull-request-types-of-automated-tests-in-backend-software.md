---
title: "No Tests - No Pull Request, Right? Types of Tests that Should Be in Your Codebase"
layout: post
date: 2017-10-09
location: Austin, Texas
description: Providing tests with your Pull Requests removes the amount of stress from your life and eventually makes you happier. Overview of unit, integration, external, and performance tests can be found in the blog post.
comments: true
tags: [development process, quality engineering]
og_image: /images/no-tests-no-pull-request-disk.png
keywords:
  - types of automated tests for backend software
  - writing tests for your code
  - making good pull requests
  - unit tests python
  - integration tests python
  - external tests python
  - performance tests python
---

<div class="image-wrapper">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/no-tests-no-pull-request-disk.png"
      alt="No Tests - No Pull Request, Right? Types of Tests that  Should Be in Your Codebase."
      class="image-right"
      width="256"
      height="320"
      layout="fixed">
  </amp-img>
</div>


As the blog post **[Pull Requests: The Good, The Bad and The Ugly](http://allyouneedisbackend.com/blog/2017/08/24/pull-requests-good-bad-and-ugly/){:target="_blank"}** claims:
> #### If you do not have time to write tests today - you will find the time for fixing bugs Friday’s night

In other words, to establish solid reliability in production tomorrow we need to invest our time today. Your need for tests for your current project depends on:
* Size of the team that maintains to the codebase: `return True if team.size > 1 else False`. Having more engineers means more views on the same items. Tests help to document the opinions how a class or function can be used.
* Size of the codebase: `return True if project.modules > 1 else False`.  You can't remember the color of socks that you wore two days ago. Can you remember everything in the project?
* Duration of development and maintenance phases of the project. The script that you run only once can perfectly live without a solid test coverage.  If you're building a system for decades - please, prepare a good legacy for the next generations of developers.

I have a strong feeling that you think that your code needs tests since you're still reading this.

In the blog post, I will guide you thru types of automated tests that should be implemented by software engineers: **unit**, **integration**, **external**, and **performance** ones. It does not cover testing efforts by quality engineers, but the article can still be valuable for them.

You will find code examples that use Python, but you do not have to know the language.

 <!--more-->

------

What is an automated test?
----

Software test is a thing that consumes the time that can be rationally used for development of unstable features. Always ask your leadership or business owners what's preferred for the product. It helps to define proper priorities.

Unexperienced software developers often think that testing it's something that should be done exclusively by quality assurance team.  I tend to disagree. Good engineers own their shit.

In a test, you call a function that is already written and or still does not exist (read more about **[Test-Driven Development](https://en.wikipedia.org/wiki/Test-driven_development){:target="_blank"})**. You pass some parameters and expect the function to return a specific value. If the value is wrong, that means that the test failed and the code is broken. *Or the test is implemented poorly.*

Some programming languages provide the ability to wrap tests into the documentation as Python does. It's called **[doctests](https://en.wikipedia.org/wiki/Doctest){:target="_blank"})**.
```
def multiply(s: str, n: int) -> str:
    """Repeats a string multiple times.

    Args:
        s (str): name to repeat.
        n (int): multiplier.

    Examples:
        >>> multiply('Backend', 2)
        'BackendBackend'
        >>> multiply('Omn', 3)
        'OmnOmnOmn'
    """
    return s * n
```
{: .language-python}

It's easier to write good tests if you test a **[pure function](https://en.wikipedia.org/wiki/Pure_function){:target="_blank"}**: the output of a function is completely determined by its inputs. Running pure function has no side effects.

Automated tests do not exist by themselves. They are executed by Continuous Integration servers like [Bamboo](https://www.atlassian.com/software/bamboo){:target="_blank"}, [Jenkins](https://www.g2crowd.com/products/jenkins/reviews){:target="_blank"}, or [Travis CI](https://travis-ci.org/){:target="_blank"}. Usually, the tests are executed for each submitted PR. If the build is green - the branch can be considered to merged into the master branch after code review. Obviously, engineers run tests locally before pushing code. Nobody likes reviewing a priory not working pull requests.

In the next sections, you can find the overview of tests that I recommend to supply with backend software.

Unit tests
----

This type of tests is the most popular and the most known. One of the right questions to ask during a job interview for a new company can be "Does your team write unit tests for new code?".

Jokes aside, the purpose of a unit test is to ensure that an atomic unit of code works as expected. Usually, the unit of code is a function or a method.
Unit tests must be small, fast, keep everything inside one process that runs a test suite. And do not interact with anything else. The type of tests is a great tool when we need to check the correctness of business rules in your code.

Here's the example of a unit test for the function `parse_fullname` that parses full name of a person to get Firstname and Lastname:

```
from unittest import TestCase

from utils import parse_fullname

class ParseFullnameTestCase(TestCase):
    def test_parse_fullname(self):
        cases = [
            ('John Doe', ('John', 'Doe'), 'first and last name'),
            ('John David Doe', ('John David', 'Doe'), 'first, middle, last name'),
            ('John David van Eck de la Nova Doe', ('John David van Eck de la Nova', 'Doe'),
                'many name parts'),
            ('John', ('John', 'John'), 'single name'),
            ('John David Doe, Jr.', ('John David', 'Doe Jr'), 'Jr. suffix'),
            ('John Doe II', ('John', 'Doe II'), 'II suffix'),
            ('Mr. John Doe', ('John', 'Doe'), 'Mr. prefix'),
            ('Вячеслав Каковський', ('Вячеслав', 'Каковський'), 'unicode chars')
        ]
        for name, expected_output, description in cases:
            self.assertEqual(parse_fullname(name), expected_output, msg='Failed for {}'.format(description))
```
{: .language-python}

The test checks if the returned value matches the expected one for each of the cases and provides the explanation when an assertion is failed.

Again, it's better if unit tests are fast. For example, execution of hundreds of unittests for production software takes seconds, rarely minutes.

How to make good unit tests without side effects?
We can use **[Dependency Injection](https://en.wikipedia.org/wiki/Dependency_injection){:target="_blank"}** to substitute objects that perform heavy operations with **[Mocks, Stubs, or Fake objects](https://martinfowler.com/articles/mocksArentStubs.html){:target="_blank"}**. Yes, your unit test should not perform I/O operations, like reading/writing data from a database, performing HTTP calls and so on. Check a unit test for the `@retry` decorator that tries to reattempt execution if an exception of specified type occurred.

```
from aiohttp import DisconnectedError
from asynctest import TestCase, CoroutineMock as Mock

from utils import retry

class RetryTest(TestCase):
    async def test_retry(self):
        self._func = Mock(return_value=200,
                          side_effect=[DisconnectedError,
                                      DisconnectedError, 200]

        @retry(DisconnectedError)
        async def get_http_status():
            return await self._func()

        res = await get_http_status()
        self.assertEqual(res, 200)
```
{: .language-python}

We use `Mock` object to introduce a function with the predefined behavior: raising  `DisconnectedError` two times and returning status code 200 that means successful HTTP-request. Thankfully, we do not have to perform the actual request to some web server and do all slow I/O work. Also, we do not need to perform some tweaks with configuring the server or load balancer to break the connection for each execution of the test.

I encourage you to read about the `retry` function in my another blog post **[Never Give Up, Retry: How Software Should Deal with Failures](http://allyouneedisbackend.com/blog/2017/09/15/how-backend-software-should-retry-on-failures/){:target="_blank"}**. I found the technique very useful during making backend that depends on various other services.

Examples from real life when a unit test is a good fit:

* all types of parsing: messages, documents, arguments, and configuration
* checking business rules and corner cases
* input validation or other verification of chains of complex if-else statements
* calculation of math formulas, like business rules for discounts
* complex data transformations from one format to another
* verification of SQL-queries compiled by ORM (do not mix with execution of the queries against a database)
* when you need to check that invocation of one function leads to a call of another one; I highly recommend to use mocks for that
* verification of firing network operations, but do not forget to replace actual I/O operations with stubs.

For most of the modern programming languages, you can find great toolbox for writing good unit tests fast. It might include:
* primitives for implementing Mocks, Stubs, and Fake objects
* hooks for running before/after a test in test suite
* utility for running a set of tests from command line
* tools for running tests under various environments, like versions of interpreter/virtual machine.

Integration tests
----

The main purpose of the type of tests - verify cooperation between various modules and components that *you develop*. Here you're encouraged to perform I/O operations in your tests, therefore, the test suites might be running slow.

These tests are focused on API contract on your subsystems as well as integration with the data storages that you use.

> #### The main feature of integration tests for me is that they do not have to run only inside one process: tested code can perform a syscall or execute a query against a real database.

Check out integration tests for `SQLAlchemyEngine` class that  implements database wrappers for the high-level methods:
* `execute`: executes SQLAlchemy query, return the number of affected rows
* `fetchone`: shorthand for fetching one DB entry
* `fetchall`: shorthand for fetching all DB entries.

```
import uuid

from asynctest import TestCase

from database import SQLAlchemyEngine, users
from utils import get_config


class DBEngineTests(TestCase):
    async def setUp(self):
        self._user_data = {'user_id': 100500,
                           'external_id': uuid.uuid4().hex,
                           'name': 'Viach'}
        self._db_engine = self._get_db_engine()
        self._user = await self._create_user(self._user_data)

    async def tearDown(self):
        async with self._db_engine.acquire() as conn:
            await conn.execute(users.delete())

    async def test_fetchone(self):
        fetched_user = await self._db_engine.fetchone(
            users.select(users.c.id == self._user['id']))
        self.assertEqual(fetched_user, self._user_data)

    async def test_execute_delete_user(self):
        rowcount = await self._db_engine.execute(
            users.delete(users.c.id == self._user['id']))
        self.assertEqual(rowcount, 1)

        fetched_user = await self._db_engine.fetchone(
            users.select(users.c.id == self._user['id']))
        self.assertIsNone(fetched_user)

    async def test_fetchone_not_exists(self):
        fetched_user = await self._db_engine.fetchone(
            users.select(users.c.id == self._user['id'] + 1))
        self.assertIsNone(fetched_user)

    async def test_fetchall(self):
        fetched_users = await self._db_engine.fetchall(
            users.select(users.c.id == self._user['id']))
        self.assertLenEqual(fetched_users, 1)
        self.assertEqual(fetched_users[0], self._user_data)

    async def _create_user(self, user_data):
        async with self._db_engine.acquire() as conn:
            await conn.execute(users.insert().values(**user_data))

            result = await conn.execute(users.select().where(
                users.c.id == user_data['id']))
            return result

    async def _get_db_engine(self):
        return await SQLAlchemyEngine.from_config(get_config()['mysql']['userbase'],
                                                  loop=self.loop)
```
{: .language-python}

I think that an integration test is a perfect idea when you need to verify database-related code:

* embedding a third-party driver for a datastore in your codebase; a smoke test that inserts a record and fetches that is usually enough
* complex queries that depend on the state of database; do not forget to set the state in a pre-test hook
* not-complex queries in the case when you do not use ORM and cannot check compiled statements (using ORM you can do this with unit tests)
* homebrew wrappers/patches of existing database drivers.

Other possible applications of integration tests from my experience:

* testing of contracts between your subsystems, like public interfaces between modules
* verification of communications between your services; say, a test that ensures that a service performs a request against another one for some task.

Note, that you still can and should use mocks to replace some parts of the software to make the establishing environment for tests easier and execution of tests faster. It helps to keep time for running the type of tests in the range between minutes and few dozens of minutes.

We reviewed unit and integration tests, the purpose of the first category is to verify that individual components work as expected; the reason to write the second ones - check that combination of the pieces that you implemented plays as a team.

But what if your product involves the software not written by your team and runs not under control of the organization? Time to look into the next category of tests.

External tests
----

You should write external tests when you need to track contracts between your software and third-party services that cannot be controlled by your team. It can be services maintained by other teams inside your company or software that runs on the infrastructure of your vendors, partners, or even competitors.

Real examples of things to be tested with an external test:
* API contracts between teams in your company
* usage of services provided by your cloud provider; it's AWS in my case;
* integrations with Developer APIs of services like [Stride](https://developer.atlassian.com/cloud/stride/){:target="_blank"}, [Hipchat](https://developer.atlassian.com/hipchat){:target="_blank"}, [Bitbucket](https://developer.atlassian.com/bitbucket/api/2/reference/){:target="_blank"}, [Trello](https://developers.trello.com/v1.0/reference){:target="_blank"}, [GitHub](https://developer.github.com/){:target="_blank"}, [Slack](https://api.slack.com/){:target="_blank"}, etc.

You might be interested why external tests are in a separate category instead of being a part of integration tests?

Firstly, not necessary that your team can fix a failure of an external test. If the system is broken on the other end - you can only file a bug report and try to prioritize it.

Secondary, since you do not control the environment on the other end some failures can be random: issues with availability, poor deployments, etc.

Check out the example of an external test for verification of code that works with Amazon SQS:

```
 class SQSTestCase(TestCase):
    async def setUp(self):
        config = get_config()
        self.client = await SQSClient.from_config(config, loop=self.loop)

    def tearDown(self):
        self.client.close()

    async def test_send_and_receive(self):
        msg = {'id': 100500,
               'description': 'some important SQS task'}
        await self.client.send_message(msg)
        result = await self.client.receive_messages()
        self.assertEqual(result, [msg])
```
{: .language-python}

In *some cases* it can be okay to ignore failures of external tests to do not block deployments. But it's still required to figure out the reason of red builds.

Performance tests
----

> #### The purpose of performance testing is to predict when we fu\*k production.

In other words, it helps to find out conditions when our algorithms, architecture, or infrastructure cannot handle load properly.
I believe that performance testing is a must for a high load system. I published a short blog **[What Is a Highload Project?](http://allyouneedisbackend.com/blog/2017/08/30/what-is-highload/){:target="_blank"}** about my definition of the term, check out if you're interested.

Look into the steps to add performance testing into your workflow.

1. **Identify** how the load might grow up. Possible cases:
* More users start using the software that you build.
* More data is sent thru your processing pipeline.
* You need to shrink capacity of your servers because of changes in your budgeting.
* All sorts of unexpected edge cases.

2. **Define** the most heavy and frequent operations. Examples:
* Insertions into data storages.
* Calculations and other CPU-bound tasks.
* Calls to external services.     

3. **Identify** how to trigger the operations above from a user's perspective. For example:
* HTTP/XMPP/your-favorite-protocol handlers.
* REST API endpoints.
* Periodic processing of collected data.

4. **Setup collecting of product metrics** for the identified operations:
* Application metrics can be collected using StatsD. The most often I use counters and timers.
* For per-instance metrics, I can recommend CollectD. Top 5 metrics to look into: Load Average, CPU, RAM, Bytes received/sent, and Free disk.

5. **Create a tool** that behaves like gazillions customers using your product and triggering the heavy operations. For a web server such actions can be:
* Establishing network connections.
* Making HTTP requests, sending data and retrieving information.

6. **Run the tooling** in the staging environment with enabled metrics collection. Roll out the load gracefully to investigate the behaviour of your system, pay attention to any spike. You also can test autoscaling of the infrastructure if you use any.  

Possible results of a successful session of performance testing:
* You know how many requests per second can be served by a particular configuration of infrastructure.
* You know how the system behaves when the limit is exceeded.
* You see the bottlenecks of the platform.
* You understand if some part of the system can be scaled.

[LocustIO](https://locust.io/){:target="_blank"} can be a good thing to start implementation of performance testing. It's written in Python, runs load tests distributed over multiple hosts and support various protocols including [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol){:target="_blank"}, [XMPP](https://en.wikipedia.org/wiki/XMPP){:target="_blank"}, and [XML-RPC](https://en.wikipedia.org/wiki/XML-RPC){:target="_blank"}.

Summary
---

In the blog post, we briefly introduced four types of tests. From my experience, the tests should be provided by the engineering teams that are actively involved in development of your product, not a separate quality engineering team.

Check out the summary about each kind of tests below.

**Unit tests:**
* Small and isolated.
* Keep everything within one process, code in tests should not lead to system calls.
* Extremely fast.
* Good fit for checking business rules.
* Run inside your development environment.

**Integration tests:**
* The main purpose - verify that various components work well together.
* Can perform I/O operations.
* Slow.
* Execution flow can be distributed across processes.
* Run in your development environment.

**External tests:**
* Ensure that reached API contracts are implemented properly.
* Involve calls to software that runs out of your direct control.
* Run between your development environment and third-party. servers.
* Tend to be very slow.

**Performance tests:**
* Help to save reputation of your business if the product can be under high load.
* Require additional preparations but worth it.
* Are executed in staging environment.
* Very very slow, should be run within scheduled windows.

I think that each mature programming language has own variation of [xUnit](https://en.wikipedia.org/wiki/XUnit){:target="_blank"}-like toolset for writing automated tests for fun and profit.

I hope you found the practical examples in the blog post useful for your team. During the last three years, our teams invested a lot of resources into providing various types of tests as a part of a pull request. We found this rewarding and valuable for our product.

The teams feel more healthy working in the environment when we have enough test coverage since we're protected from code regression.

**What types of tests do you provide for your code?**

**How much time do you spend dealing with fixing features that were delivered a while ago?**
