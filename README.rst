Django GraphQL JWT
==================

|Pypi| |Build Status| |Codecov| |Code Climate|


`JSON Web Token <https://jwt.io/>`_ authentication for `Django GraphQL <https://github.com/graphql-python/graphene-django>`_


Installation 【 安装 】
------------------------

Install last stable version from Pypi:

【 从Pypi安装最新稳定版 】

::

    pip install django-graphql-jwt

Add ``AuthenticationMiddleware`` middleware to your *MIDDLEWARE* settings:

【 添加 ``AuthenticationMiddleware`` 中间件到 *MIDDLEWARE* 设置项中 】

.. code:: python

    MIDDLEWARE = [
        ...
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        ...
    ]

Add ``JSONWebTokenMiddleware`` middleware to your *GRAPHENE* settings:

【 添加 ``JSONWebTokenMiddleware`` 中间件到 *GRAPHENE* 设置项中 】

.. code:: python

    GRAPHENE = {
        'SCHEMA': 'mysite.myschema.schema',
        'MIDDLEWARE': [
            'graphql_jwt.middleware.JSONWebTokenMiddleware',
        ],
    }

Add ``JSONWebTokenBackend`` backend to your *AUTHENTICATION_BACKENDS*:

【 添加 ``JSONWebTokenBackend`` 中间件到 *AUTHENTICATION_BACKENDS* 设置项中 】

.. code:: python

    AUTHENTICATION_BACKENDS = [
        'graphql_jwt.backends.JSONWebTokenBackend',
        'django.contrib.auth.backends.ModelBackend',
    ]


Schema 【 图 】
--------------------

Add *django-graphql-jwt* mutations to the root schema:

【 添加 *django-graphql-jwt* mutations 到根 schema 中 】

.. code:: python

    import graphene
    import graphql_jwt


    class Mutation(graphene.ObjectType):
        token_auth = graphql_jwt.ObtainJSONWebToken.Field()
        verify_token = graphql_jwt.Verify.Field()
        refresh_token = graphql_jwt.Refresh.Field()


    schema = graphene.Schema(mutation=Mutation)


Documentation  【 文档 】
--------------------------

Fantastic documentation is available at https://django-graphql-jwt.domake.io.
【 官方文档： https://django-graphql-jwt.domake.io 】


.. |Pypi| image:: https://img.shields.io/pypi/v/django-graphql-jwt.svg
   :target: https://pypi.python.org/pypi/django-graphql-jwt
   :alt: Pypi

.. |Build Status| image:: https://travis-ci.org/flavors/django-graphql-jwt.svg?branch=master
   :target: https://travis-ci.org/flavors/django-graphql-jwt
   :alt: Build Status

.. |Codecov| image:: https://codecov.io/gh/flavors/django-graphql-jwt/branch/master/graph/badge.svg
   :target: https://codecov.io/gh/flavors/django-graphql-jwt
   :alt: Codecov

.. |Code Climate| image:: https://api.codeclimate.com/v1/badges/c79a185d546f7e34fdd6/maintainability
   :target: https://codeclimate.com/github/flavors/django-graphql-jwt
   :alt: Codeclimate
