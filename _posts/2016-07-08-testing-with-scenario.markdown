---
layout: post
title: Django Testing with Scenario
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
    def setUp(self):
        self.username = 'johnny'
        self.email = 'test@test.com'
        self.password = 'password'        
        self.test_user = User.objects.create_user(self.username, self.email, self.password)
    
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
# PYTHON
from contextlib import contextmanager

# THIRD PARTY APPS
from django.test import TestCase

# OUR WEBAPP
from .factories import UserFactory 

class ScenarioTestCase(TestCase):
    def create_scenario(self, user):
        @contextmanager
        def scenario():
            if user is not None:
                self.client.login(username=user.username, password='password')
            yield
            if user is not None:
                self.client.logout()
        return scenario

    def setUp(self):
        self.user1 = UserFactory.create()
        # Add here more users if needed
        
        self.no_user_logged_in_scenario = self.create_scenario(None)
        self.user1_logged_in_scenario = self.create_scenario(self.user1)
        # Add here any other scenario you'd like
```

To make things a bit easier, I used a really simple `UserFactory` using [Factory Boy](https://factoryboy.readthedocs.io/en/latest/).

Here is the `factories.py` file if you're a bit lost:

```python
import factory

# DJANGO
from django.conf import settings


class UserFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = settings.AUTH_USER_MODEL

    username = factory.Sequence(lambda n: "User %03d" % n)
    email = factory.LazyAttribute(lambda obj: '%s@example.com' % obj.username)

    @classmethod
    def _generate(cls, create, attrs):
        """Override the default _generate() to set the password."""
        user = super()._generate(create, attrs)
        user.set_password('password')
        user.save()
        return user
```

Note that all my test users have a **super** safe password now.

We have our factory all set and a brand new `ScenarioTestCase` in our test file ready to go. Let's try it out !


```python
class SimpleTest(ScenarioTestCase):

    def test_access(self):
        with self.user1_logged_in_scenario():
            self.assertEqual(self.client.get('/customer/details/').status_code, 200)
        
        with self.no_user_logged_in_scenario():
            self.assertEqual(self.client.get('/customer/details/').status_code, 403)

```

That's all folks! Cheers ! :beers:

