.. _installation_and_configuration:

Installation and Configuration
==============================

Installation
------------
If not already done, install the **Redis server**, using the installation tool offered by the
operating system, such as ``aptitude``, ``yum``, ``port`` as  or install `Redis from source`_.

Start the Redis service on your host::

  $ sudo service redis-server start

Check if Redis is up and accepting connections::

  $ redis-cli ping
  PONG

Install **Django Websocket for Redis**. The latest stable release can be found on PyPI::

  pip install django-websocket-redis

or the newest development version from github::

  pip install -e git+https://github.com/jrief/django-websocket-redis#egg=django-websocket-redis

**Websocket for Redis** does not define any database models. It can therefore be installed without
any database synchronization.


Dependencies
------------
* Django_ >=1.5
* `Python client for Redis`_
* uWSGI_ >= 1.9.20
* gevent_ >=1.0
* greenlet_ >=0.4.1
* optional, but recommended: wsaccel_ >=0.6


Configuration
-------------
Add ``"ws4redis"`` to your project's ``INSTALLED_APPS`` setting::

  INSTALLED_APPS = (
      ...
      'ws4redis',
      ...
  )

Specify the URL that distinguishes websocket connections from normal requests::

  WEBSOCKET_URL = '/ws/'

If the Redis datastore uses connection settings other than the defaults, use this dictionary to
override these values::

  WS4REDIS_CONNECTION = {
      'host': 'redis.example.com',
      'port': 16379,
      'db': 17,
      'password': 'verysecret',
  }

.. note:: Specify only the values, which deviate from the default.

**Websocket for Redis** can be configured with ``WS4REDIS_EXPIRE``, to additionally persist messages
published on the message queue. This is advantageous in situations, where clients shall be able
to access the published information after reconnecting the websocket, for instance after a page
is reloaded.

This directive sets the number in seconds, each received message is persisted by Redis, additionally
of being published on the message queue::

  WS4REDIS_EXPIRE = 7200

Override ``ws4redis.store.RedisStore`` with a customized class, in case you need an alternative
implementation of that class::

  WS4REDIS_STORE = 'myapp.redis_store.RedisStore'

This directive is required during development and ignored in production environments. It overrides
Django's internal main loop and adds a URL dispatcher in front of the request handler::

  WSGI_APPLICATION = 'ws4redis.django_runserver.application'


Check your Installation
-----------------------
With **Websockets for Redis** your Django application has immediate access to code written for
websockets. Change into the ``examples`` directory and start a sample chat server::

  ./manage.py syncdb
  ... create database tables
  ... answer the questions
  ./manage.py runserver

Point a browser onto http://localhost:8000/chat/, you should see a simple chat server. Enter
a message and send it to the server. It should be echoed immediately on the billboard.

Point a second browser onto the same URL. Now each browser should echo the message entered into
input field.

In the examples directory, there are two chat server implementations, which run out of the box.
One simply broadcasts messages to every client listening on that same websocket URL. The other
chat server can be used to send messages to specific users logged into the system. Use these
demos as a starting point for your application.

Replace memcached with Redis
----------------------------
Since Redis has to be added as an additional service into the current infrastructure, at least
another service, can be safely removed: memcached is required by typical Django installations and
is used for caching and session storage.

Its beyond the scope of this documentation to explain how to set up a caching and/or session store
using Redis, but check django-redis-sessions_ and django-redis-cache_ for details. Here is a
description on how to use `Redis as Django session store and cache backend`_.

.. _Redis from source: http://redis.io/download
.. _github: https://github.com/jrief/django-websocket-redis
.. _Django: http://djangoproject.com/
.. _Python client for Redis: https://pypi.python.org/pypi/redis/
.. _uWSGI: http://projects.unbit.it/uwsgi/
.. _gevent: https://pypi.python.org/pypi/gevent
.. _greenlet: https://pypi.python.org/pypi/greenlet
.. _wsaccel: https://pypi.python.org/pypi/wsaccel
.. _django-redis-sessions: https://github.com/martinrusev/django-redis-sessions
.. _django-redis-cache: https://github.com/sebleier/django-redis-cache
.. _Redis as Django session store and cache backend: http://michal.karzynski.pl/blog/2013/07/14/using-redis-as-django-session-store-and-cache-backend/
