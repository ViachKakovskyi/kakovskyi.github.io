---
title: "Pull Requests: The Good, The Bad and The Ugly"
layout: post
date: 2017-08-24
location: Austin, Texas
description: |
 Merging good pull requests and declining ugly is essential for success of your product. What about bad ones?
comments: true
tags: [development process]
og_image: /images/the-good-the-bad-the-ugly-logo-small.png
keywords:
  - bitbucket github gitlab code review
  - good pull request example
  - bad pull request example
  - ugly pull request example
  - pull requests tips and tricks
---

<div class="image-wrapper">
  <amp-img
      media="(min-width: 550px)"
      src="{{ site.cdn.http }}/images/the-good-the-bad-the-ugly-logo-small.png"
      alt="Pull Requests: The Good, The Bad and The Ugly"
      class="image-right"
      width="250"
      height="345"
      layout="fixed">
  </amp-img>
</div>

I remember that at my first paid software job we did not have a [version control system](https://en.wikipedia.org/wiki/Version_control){:target="_blank"}: all code was uploaded to a folder on FTP server.

[Source Code Management](https://git-scm.com/){:target="_blank"} was not very sophisticated: we just had the previous revision of the most important source files suffixed with `.old` in the same directory. Now having *'just Git'* without a fancy dashboard like the one supplied by [Bitbucket](https://bitbucket.org/){:target="_blank"}, [GitHub](https://github.com/){:target="_blank"}, or [GitLab](https://gitlab.com){:target="_blank"} does not look suitable for me. The tools save a lot of time and are extremely convenient.

Working on our product, a software engineer submits pull requests almost every day. During the last year, I spent approximately 200 hours doing code review - it's more than 1 month of work!

**I believe that merging good pull requests and declining ugly is essential for the success of your product**.

What about bad ones? Well, we can do some work to make on them either good or ugly. Let's review the examples representing different aspects of a pull request. Some ideas are explained using `Python`, but they are applicable for any other *non-[esoteric](https://en.wikipedia.org/wiki/Esoteric_programming_language){:target="_blank"}* programming language.

<!--more-->

----

Writing description for a PR
-----


**Ugly.**

~~~
No description (tumbleweed)
~~~

Name of a PR and its description - it's the first thing which your teammate sees. She or he kindly switched from current task to help you with building high-quality software. Love your teammates. Do not let them be distracted. Be good team fellow and drop a line.

**Bad.**

~~~~~
Security fixes
--------------
Make component XXX protected from the OWASP Top Security Risks:
* A-1
* A-3
* A-8
~~~~~

Isn't it look better than the previous one? Of course, it is! The information helps to understand which teammates should be involved into code review process: now we can see that it's not mandatory for people without expertise in Software Security field. This is very helpful within large teams.

At some projects, such description is *good enough*, but I think that we should do it better: this description still keeps a reviewer out of context.

**Good.**

~~~~~
JRA-2017: Add protection from some of Top OWASP 2017 Security Risks. Part 1
--------------
Make component XXX protected from the risks:
* A-1. Injection. Partial support: only SQL injection.
* A-3. Cross-Site Scripting (XSS)
* A-8. Cross-Site Request Forgery (CSRF)

Link: https://www.owasp.org/index.php/Top_10_2017-Top_10
~~~~~

You can notice the improvements in the description:
* The PR is linked to some ticket in your issue tracking system. JIRA and Trello are the industry leaders. Bitbucket, GitHub, and GitLab kindly provided integrations with the tools. I recommend to use them, it's very easy: you just need to prefix name of the PR with ticket's shortcut and link to the issue is rendered automatically. This is VERY helpful for reviewers.
* We're more specific about the version of OWASP document which is related to the task. And we specified a link to the needed revision. Now our teammates do not need to google that, make assumptions or ask additional questions.
* We explained A-1, A-3, and A-8. Now the risks which are addressed in the pull request are explicitly specified. We also noted, that for `Injection` part only SQL injection is addressed.

The way from Ugly description to a Good one takes two minutes of your time but saves 10-15 minutes of time for each teammate.

Code design
-----

This section is not a place for holy wars about different programming paradigms. But if you're interested in a general overview - check out [the article from LMA](http://cs.lmu.edu/~ray/notes/paradigms/){:target="_blank"}. We wanna look into code design from a high-level perspective.

**Ugly.** *Add a new piece of code ignoring style of everything around that.*


 In Python, it's very easy since the language supports multiple programming paradigms. At some point in my career I faced with the PR when inside the code, which is organized due to OOP guidelines, somebody wrote a couple of layers of lambda functions without any reasoning. Well, the code was mine and I'm feeling embarrassed because of that :). It was a stupid thing - I remember the day when I needed to make a change in the module.

**Bad.** *The new code is formatted in the same fashion as the previous code.*

> #### What? Why it's considered as bad, Viach? Are you mad?  

Having new code looking the same as previous one it's essential, but not enough to be good. Some reviewers agree to merge new code considering only that metric. It leads to a breach when a poor, but well-formatted code is deployed and caused some unexpected behavior in production.


**Good.** *Code follows the architectural principles accepted in the project/repo.
New functionality is implemented by reusing existing components or by new abstractions which do not contradict with the guidelines.*

Maintenance of different styles of doing the same thing is an unacceptable luxury for commercial software projects. Avoid growing of project's toolset without growing of toolset's functional capabilities. It should be one and only one way to do a thing.

Automated testing
-----

**Ugly.** *Submitting a PR without tests*.

New functionality should be supplied with tests, bug fixes should be covered with tests. If you do not have time to write tests today - you will find the time for fixing bugs Friday's night :(. Negotiate this with your management and include time for writing tests to your schedule

**Bad.** *Making a PR which has **some** tests*.

This is a dangerous spot. Reviewers might be confused.

> #### Oh, we have some tests, looks like it's OK to merge it.

Having only one test for a function is not a rule. Sometimes you need more, sometimes you do not need tests at all (for example, if you're just wrapping functions written by others).

**Good.** *Providing comprehensive tests both for the happy path and corner cases for the new/changed functionality in your PR*. Consider writing tests for API of your functions before the actual implementation. I liked the trick so much since it helped me to make a design better.

Refactoring of existing code
-----

**Ugly.** *Refactoring of the code which is not covered by tests.*

> #### WTF? Refactoring without automated tests? AAAAAAAAH

Yes, it happens. And this is definitely ugly. Do not do that.

**Bad.** *Doing a massive refactoring as a part of some other feature ticket*.

With the best intentions, you made the code better readable and maintainable during work on your current task. Great job! But why your business might be not happy? Firstly, delivery of the original task was delayed since you worked on the refactoring of another part of code. Secondary, other engineers might be working on a code change in the same place/file. He or she might be not happy to rebase working branch to unexpectedly consume your changes.

**Good.** *Extracting of refactoring to a separate ticket. Negotiation of the change with the team. Doing the work as a part of Technical Debt effort.*

Refactoring is a good thing, but your team might be not ready for the goodness. Speak up and prioritize the work.

Maintaining docstrings
-----

In Python usage of [docstrings](https://en.wikipedia.org/wiki/Docstring){:target="_blank"} is a thing. Documentation for libraries or HTTP API endpoints can be generated using them.

**Ugly.**
~~~~~~
def do_complex_thing(self, *args, **kwargs):
    """
    TODO: add a docstring
    """
~~~~~~
{: .language-python}

My recommendation: *'public'* functions with more than 30 LOC should have docstrings. They can be used to explain not straightforward details of implementation or link code with the related ticket in issue tracking system.

**Bad.**

~~~~~~
def multiply(a, b):
    """Adds two integers.

    Args:
        a (str): The first parameter.
        b (int): The second parameter.

    Returns:
        int: Sum.
    """
    return a * b
~~~~~~
{: .language-python}

In this example, you can see one of the sins of developers: **copypasta**. Name of the function, code, and docstring do not match. It can be very confusing for other contributors.

Possible options what the function means:

* The function should return *multiplication of 2 integers*. In this instance, docstring should be fixed: description of the function, type of the first parameter and description of returned value.
* The function should return *sum of 2 integers*. Function name should be `sum` and code has a typo: `*` must be replaced with `+`.  
* The function should return *string repeated several times*. In that case docstring should be improved: description of function and type/description of returned value.

Any docstring which is outdated or not actual leads to a problem with maintaining your code.

> #### OMG! It's not at an ice cream! Do not want!

**Good.**

~~~~~~
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
~~~~~~
{: .language-python}

A few reasons why this version is better:
* the docstring contains actual information about the function
* we have an example of usage for the code
* we use [type annotations](https://www.python.org/dev/peps/pep-3107/){:target="_blank"} to specify types of parameters and returned value. It's used not only to explain the code but also consumed by static code analyzers.   

Tracking and tracing
-----

Logging and sending metrics it's a green field for awkward moments.

**Ugly.**
~~~~~~
print('DEBUG: {}'.format(password))
~~~~~~
{: .language-python}

Have you submitted something like that to review? I've done it :) Hopefully, it was caught by my teammates. Yes, having user's credentials logged somewhere is not very cool as well as having `print` statements in your production code.

**Bad.**

~~~~~~
import logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# reading from database
logger.info('Error during retrieving data from database.')
# doing some low-level task
logger.info('DevVM only: the function XXX is called with parameter %s')
~~~~~~
{: .language-python}

Here we do not use logging levels properly. For the first message, we should use `logger.error` and for the second one - `logger.debug`. Also, I think that in some cases it's reasonable to have several loggers for different parts of your application. See the example below.

**Good.**

~~~~~~
import logging
import logging.config

# Specifying several loggers.
# Example is taken from aiohttp
access_logger = logging.getLogger('aiohttp.access')
client_logger = logging.getLogger('aiohttp.client')
internal_logger = logging.getLogger('aiohttp.internal')
server_logger = logging.getLogger('aiohttp.server')

# Customization of logger
logging.config.dictConfig({
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'standard': {
            'format': '%(asctime)s [%(levelname)s] %(name)s: %(message)s'
        },
    },
    'handlers': {
        'default': {
            'level':'INFO',
            'class':'logging.StreamHandler',
        },
    },
    'loggers': {
        '': {
            'handlers': ['default'],
            'level': 'INFO',
            'propagate': True
        }
    }
})

~~~~~~
{: .language-python}

Customization of logger's configuration and having per-component loggers are good.
I recommend reading the  [interesting post](https://fangpenlin.com/posts/2012/08/26/good-logging-practice-in-python/){:target="_blank"} about proper logging in Python. And the official [logging tutorial](https://docs.python.org/3/howto/logging.html){:target="_blank"}.

Making UX changes
-----

> #### Backend interview: Well, you balanced the tree really great.
#### Work: Could you please move the button 3px to the left?

This blog is about backend software engineering, but sometimes we need to modify web UI. Since frontend is not one of my core competencies, I cannot provide an *Ugly* example :).

**Bad.** *Description of pull request with UX does not have any difference from a backend-only one.*
I personally do not like this approach, because it's not obvious what's changed. But in the teams which make UX changes on daily basis, it might be okay.

**Good.** *A PR is has a screenshot attached.* If we have only one screenshot, it usually shows the new state of UX.

**Awesome.** *Pull Request has images which show the UX both before and after*. I think that it's fine to be a bit verbose. If I were your teammate I would be thankful for that. Note, that UX changes may occur not only when you edit HTML or CSS directly.

Summary
-----

Pull requests are about teamwork. If you're not interested in opinions of your fellows - there is no need in PRs, right? You can just merge your branch into master.

A team is healthy if every contributor cares about making code readable and maintainable by others. You can note that way from **Ugly** to **Bad** pull requests is not hard, but moving from **Bad** to **Good** one is the key to success for making commercial software projects.


**What are your recommendations related to making good pull requests?**
