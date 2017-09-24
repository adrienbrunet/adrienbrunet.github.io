---
layout: post
title: Django Views Testing with Scenario
categories: [django]
tags: [django, testing]
comments: true
description: Django Views Testing with Scenario.
---

# Django Views Testing with Scenario
:tada: First Blog Post Ever ! :tada:

_Feels weird. Not even sure how to markdown properly but here we are._

I wanted to share a small trick I use quite often when testing views.

Sometimes, your views heavily depends on the logged in user.

Let's say our tests look like this right now:

```python
from django.test import TestCase

class SimpleTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        # Set up data for the whole TestCase
        cls.username = 'johnny'
        cls.email = 'test@test.com'
        cls.password = 'password'        
        cls.test_user = User.objects.create_user(cls.username, cls.email, cls.password)
    
    def test_details(self):
        self.client.login(username=self.username, password=self.password)
        response = self.client.get('/customer/details/')
        self.assertEqual(response.status_code, 200)

    def test_index(self):
        self.client.login(username=self.username, password=self.password)
        response = self.client.get('/customer/index/')
        self.assertEqual(response.status_code, 200)
```
It can quickly become cumbersome to handle multiple users over and over in your tests and it's not quite clear what's "johnny" able to do.

You also might need to force your user to log out, do something, log with another user etc...

What if you need to test the same bit of code with different users with different level of access? 

Here's my solution :gift: let's use a **context manager**.

```python
from contextlib import contextmanager

from django.test import TestCase

from .factories import UserFactory 

class ScenarioTestCase(TestCase):
    @contextmanager
    def log_user(self, user=None):
        if user is not None:
            self.client.force_login(user)
        try:
            yield
        finally:
            if user is not None:
                self.client.logout()
    
    @classmethod
    def setUpTestData(cls):
        cls.user1 = UserFactory.create()
        # Add here more users if needed
    
    def setUp(self):
        self.no_user_logged_in_scenario = cls.log_user(None)
        self.user1_logged_in_scenario = cls.log_user(self.user1)
        # Add here any other scenario you'd like
```

To make things a bit easier, I used a really simple `UserFactory` using [Factory Boy](https://factoryboy.readthedocs.io/en/latest/).

Here is the `factories.py` file if you're a bit lost:

```python
import factory

from django.conf import settings
from django.contrib.auth.hashers import make_password


class UserFactory(factory.django.DjangoModelFactory):
    password = make_password('password')
    username = factory.Sequence(lambda n: "User %03d" % n)
    email = factory.LazyAttribute(lambda obj: '%s@example.com' % obj.username)
    
    class Meta:
        model = settings.AUTH_USER_MODEL
```

Note that all my test users have a **super** safe password now.

We have our factory all set and a brand new `ScenarioTestCase` in our test file ready to go. Let's try it out !


```python
class SimpleTest(ScenarioTestCase):

    def test_access(self):
        with self.user1_logged_in_scenario:
            self.assertEqual(self.client.get('/customer/details/').status_code, 200)
        
        with self.no_user_logged_in_scenario:
            self.assertEqual(self.client.get('/customer/details/').status_code, 403)
            
        # we can also use the contextmanager directly
        with self.log_user(self.user1):
            ...

```

That's all folks! Cheers ! :beers:

**Update 1**: [Twidi](https://github.com/twidi) advised me to use `setUpTestData` instead of `setUp` to handle user creation. This allows us to speed up the tests a bit, `setUpTestData` is only called once for the whole test class. [Read More..](https://docs.djangoproject.com/en/1.9/topics/testing/tools/#testcase) :+1:

**Update 2**: [@ewjoachim](https://github.com/ewjoachim) pointed out in the comments that I was using a wrapper to create scenario (~ like a factory), which was quite pointless here, and that it will be far better with a `try/finally` for the `yield` to ensure the user is logged out in all case after the tests. Removing the wrapper around the contextmanager, we're even able to call it directly now. I renamed `create_scenario` to `log_user`, this should be more explicit. :+1:

A thousand thanks for your comments and improvements ! Also, this post may be better using [pytest](http://pytest.org/latest/)... But that will be for another post !

