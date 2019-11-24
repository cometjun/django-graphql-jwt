Authentication 【 验证方式 】
================================

*Django-graphql-jwt* uses a `Graphene middleware <https://docs.graphene-python.org/en/latest/execution/middleware/>`_ to hook the authenticated user into *context* object. The simple, raw way to limit access to data is to check ``info.context.user.is_authenticated``

【 *Django-graphql-jwt* 使用 `Graphene 中间件 <https://docs.graphene-python.org/en/latest/execution/middleware/>`_ 去把握用户验证登录的 *context* 对象。限制数据访问权限比较简单原始的方式是获取 info.context.user.is_authenticated 的值】

::

    import graphene


    class Query(graphene.ObjectType):
        viewer = graphene.Field(UserType)

        def resolve_viewer(self, info, **kwargs):
            user = info.context.user
            if not user.is_authenticated:
                raise Exception('Authentication credentials were not provided')
            return user


As a shortcut, you can use :doc:`decorators<decorators>` for your *resolvers* and *mutations*.

【 还有一个快捷方式，可以在需要控制权限时在 *resolvers* 和 *mutations* 方法前添加装饰器 :doc:`decorators<decorators>` 】

HTTP header  【 HTTP 请求头部 】
-----------------------------------

Now in order to access protected API you must include the ``Authorization`` HTTP header:

【 当进入需登录访问API时，必须要添加 ``Authorization`` HTTP 请求头部 】

.. code-block:: http

    POST / HTTP/1.1
    Host: domake.io
    Authorization: JWT <token>
    Content-Type: application/json;


Per-cookie 【 cookie中使用 】
----------

When a token is requested and ``jwt_cookie`` decorator is set, the response will set the given cookie with the token string

【 当使用 ``jwt_cookie`` 请求 token 时，response 将 token 字符串设置给当前网站 cookie 】

::

    from django.urls import path

    from graphene_django.views import GraphQLView
    from graphql_jwt.decorators import jwt_cookie

    urlpatterns = [
        path('graphql/', jwt_cookie(GraphQLView.as_view())),
    ]


If the ``jwt_cookie`` decorator is set, consider adding `CSRF middleware <https://docs.djangoproject.com/es/2.1/ref/csrf/>`_ ``'django.middleware.csrf.CsrfViewMiddleware'`` to provide protection against `Cross Site Request Forgeries <https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)>`_.

【 若使用 ``jwt_cookie`` 需考虑添加 `CSRF 中间件 <https://docs.djangoproject.com/es/2.1/ref/csrf/>`_ ``'django.middleware.csrf.CsrfViewMiddleware'`` 去保护防止跨域攻击 `Cross Site Request Forgeries <https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)>`_ 】

Per-argument  【 参数中使用 】
--------------------------------------

Another option to send the *token* is using an argument within the *GraphQL* query, being able to send a batch of queries authenticated with different credentials.

【另外，还可以通过在 *GraphQL* 请求的参数中提交 *token* ，使用不同的 credentials 发送一批需身份验证的请求 】

*Django-graphql-jwt*  looks for the *token* in the list of arguments sent and if it does not exists, it looks for the token in the HTTP header.

【 *Django-graphql-jwt* 会在请求参数中查找 *token* ，如果不存在，则在 HTTP 请求头部中查找 token 】

Settings  【 设置 】
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Enable the argument authentication in your settings:

【 在设置中打开参数验证方式 】

::

    GRAPHQL_JWT = {
        'JWT_ALLOW_ARGUMENT': True,
    }


Schema  【 图 】
~~~~~~~~~~~~~~~~

Add the *token* argument in any of your fields using the same name defined in ``JWT_ARGUMENT_NAME`` setting

【 使用设置 ``JWT_ARGUMENT_NAME`` 中已定义的 token 名字作为参数传递 token 】

::

    import graphene
    from graphql_jwt.decorators import login_required


    class Query(graphene.ObjectType):
        viewer = graphene.Field(UserType, token=graphene.String(required=True))

        @login_required
        def resolve_viewer(self, info, **kwargs):
            return info.context.user


Queries  【 请求 】
~~~~~~~~~~~~~~~~~~~~~~~

Send the token as another variable within the query:

【 请求中发送 token 参数的另一个方式 】

::

    query GetViewer($token: String!) {
      viewer(token: $token) {
        username
        email
      }
    }


Authenticate using **multiple credentials**:

【 使用多个**credentials** 验证 】

::

    query GetUsers($tokenA: String!, $tokenB: String!) {
      viewerA: viewer(token: $tokenA) {
        username
        email
      }
      viewerB: viewer(token: $tokenB) {
        username
        email
      }
    }
