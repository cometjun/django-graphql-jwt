Relay
=====

原文：Complete support for Relay.

翻译：完全支持 `Relay <https://facebook.github.io/relay/>`_

Schema 图
---------

原文：Add mutations to the root schema:

翻译：添加 mutations 到根 schema

::

    import graphene
    import graphql_jwt


    class Mutation(graphene.ObjectType):
        token_auth = graphql_jwt.relay.ObtainJSONWebToken.Field()
        verify_token = graphql_jwt.relay.Verify.Field()
        refresh_token = graphql_jwt.relay.Refresh.Field()

        # Long running refresh tokens
        revoke_token = graphql_jwt.relay.Revoke.Field()


    schema = graphene.Schema(mutation=Mutation)


Queries 请求
------------

原文：

Relay mutations only accepts one argument named input.

* ``tokenAuth`` to authenticate the user and obtain a JSON Web Token:



翻译：

Relay mutations 只接受一个名称为 *input* 的参数.


* ``tokenAuth`` 可以认证用户和获取 **JSON Web Token** 密钥:

  ::

      mutation TokenAuth($username: String!, $password: String!) {
        tokenAuth(input: {username: $username, password: $password}) {
          token
        }
      }

原文：

* verifyToken to validate the token and obtain the token payload:

翻译：

* ``verifyToken`` 可以验证 *token* 和获取 *token payload*:

  ::

      mutation VerifyToken($token: String!) {
        verifyToken(input: {token: $token}) {
          payload
        }
      }


Single token refresh 更新单个 token
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* ``refreshToken`` 通过一个 **未过期token** 续订一个新的 *token*:

  ::

      mutation RefreshToken($token: String!) {
        refreshToken(input: {token: $token}) {
          token
          payload
        }
      }


Long running refresh tokens  长期运行 refresh token
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* ``refreshToken`` to obtain a brand new *token* with renewed expiration time for **non-expired tokens**:

* [``refreshToken`` 重新获取你的 *token*, 在已认证期间使用 ``refreshToken`` 重新获取 *token*:]

  ::

      mutation RefreshToken($refreshToken: String!) {
        refreshToken(input: {refreshToken: $refreshToken}) {
          token
          refreshToken
          payload
        }
      }

* ``revokeToken`` to revoke a valid ``refreshToken``. The invalidation takes place immediately, and the ``refreshToken`` cannot be used again after the revocation:

* [``revokeToken`` 撤销有效的已获取token。撤销马上生效，并且不能使用``refreshToken``再次获取token]

  ::

      mutation RevokeToken($refreshToken: String!) {
        revokeToken(input: {refreshToken: $refreshToken}) {
          revoked
        }
      }


Customizing 客制化
------------------

If you want to customize the ``ObtainJSONWebToken`` behavior, you'll need to customize the ``resolve()`` method on a subclass of:

【若你希望自定义``ObtainJSONWebToken``行为，你可以通过继承这个类，重写``resolve()``方法】

  graphql_jwt.relay.JSONWebTokenMutation

::

    import graphene
    import graphql_jwt


    class ObtainJSONWebToken(graphql_jwt.relay.JSONWebTokenMutation):
        user = graphene.Field(UserType)

        @classmethod
        def resolve(cls, root, info):
            return cls(user=info.context.user)

Authenticate the user and obtain a **JSON Web Token** and the *user id*

【 验证用户身份并获取 **JSON Web Token** 和 *user id* 】

::

    mutation TokenAuth($username: String!, $password: String!) {
      tokenAuth(input: {username: $username, password: $password}) {
        token
        user {
          id
        }
      }
    }
