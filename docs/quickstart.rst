Quickstart  【 快速开始 】
=============================

Dependencies  【 依赖 】
--------------------------

* Django ≥ 1.11


Installation  【 安装 】
--------------------------

Install last stable version v\ |version| from Pypi

【 从Pypi安装稳定版 v\ |version| 】

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

Add ``JSONWebTokenMiddleware`` middleware to your *GRAPHENE* settings

【 添加 ``JSONWebTokenMiddleware`` 中间件到 *GRAPHENE* 设置项中 】

::

    GRAPHENE = {
        'SCHEMA': 'mysite.myschema.schema',
        'MIDDLEWARE': [
            'graphql_jwt.middleware.JSONWebTokenMiddleware',
        ],
    }

Add ``JSONWebTokenBackend`` backend to your *AUTHENTICATION_BACKENDS*

【 添加 ``JSONWebTokenBackend`` 中间件到 *AUTHENTICATION_BACKENDS* 设置项中 】

::

    AUTHENTICATION_BACKENDS = [
        'graphql_jwt.backends.JSONWebTokenBackend',
        'django.contrib.auth.backends.ModelBackend',
    ]


Schema 【 图 】
--------------------

Add mutations to the root schema

【 添加 mutations 到根 schema 中 】

::

    import graphene
    import graphql_jwt


    class Mutation(graphene.ObjectType):
        token_auth = graphql_jwt.ObtainJSONWebToken.Field()
        verify_token = graphql_jwt.Verify.Field()
        refresh_token = graphql_jwt.Refresh.Field()


    schema = graphene.Schema(mutation=Mutation)


Queries
-------

* ``tokenAuth`` to authenticate the user and obtain a **JSON Web Token**.

* 【 ``tokenAuth`` 可以认证用户和获取 **JSON Web Token** 密钥: 】

  The mutation uses your User's model `USERNAME_FIELD <https://docs.djangoproject.com/en/2.0/topics/auth/customizing/#django.contrib.auth.models.CustomUser>`_, which by default is ``username``:

【 mutation 默认使用了名为 username 的用户模型 `USERNAME_FIELD <https://docs.djangoproject.com/en/2.0/topics/auth/customizing/#django.contrib.auth.models.CustomUser>`_ 】

  ::

      mutation TokenAuth($username: String!, $password: String!) {
        tokenAuth(username: $username, password: $password) {
          token
        }
      }


* ``verifyToken`` to validate the *token* and obtain the *token payload*:

* 【 ``verifyToken`` 可以验证 *token* 和获取 *token payload*: 】

  ::

      mutation VerifyToken($token: String!) {
        verifyToken(token: $token) {
          payload
        }
      }


* ``refreshToken`` to obtain a brand new *token* with renewed expiration time:

* 【 ``refreshToken`` 通过已登录的 token 续订一个新的 *token*: 】

:doc:`Configure your refresh token <refresh_token>` scenario and set to True the :doc:`JWT_VERIFY_EXPIRATION<settings>` setting.

【 根据setting文件选项 :doc:`JWT_VERIFY_EXPIRATION<settings>` 中设置刷新token :doc:`Configure your refresh token <refresh_token>` 为 ``True``. 】
