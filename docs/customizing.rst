Customizing
===========

If you want to customize the ``ObtainJSONWebToken`` behavior, you'll need to customize the ``resolve()`` method on a subclass of:

【 若你希望自定义``ObtainJSONWebToken``行为，你可以通过继承这个类，重写``resolve()``方法 】

  graphql_jwt.JSONWebTokenMutation

::

    import graphene
    import graphql_jwt


    class ObtainJSONWebToken(graphql_jwt.JSONWebTokenMutation):
        user = graphene.Field(UserType)

        @classmethod
        def resolve(cls, root, info, **kwargs):
            return cls(user=info.context.user)

Authenticate the user and obtain a **JSON Web Token** and the *user id*

【 验证用户身份并获取 **JSON Web Token** 和 *user id* 】

::

    mutation TokenAuth($username: String!, $password: String!) {
      tokenAuth(username: $username, password: $password) {
        token
        user {
          id
        }
      }
    }
