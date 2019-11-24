Decorators  【 装饰器 】
============================

@login_required
---------------

  .. autodata:: graphql_jwt.decorators.login_required

As a shortcut, you can use the ``login_required()`` decorator::

【 可以使用 ``login_required()`` 装饰器验证登录 】

    import graphene
    from graphql_jwt.decorators import login_required


    class Query(graphene.ObjectType):
        viewer = graphene.Field(UserType)

        @login_required
        def resolve_viewer(self, info, **kwargs):
            return info.context.user

* If the user isn't logged in, raise ``PermissionDenied`` exception.

*【 未登录会抛出 ``PermissionDenied`` 异常 】

* If the user is logged in, execute the function normally.

*【 若已登录则正常运行函数 】

@user_passes_test
-----------------

  .. autofunction:: graphql_jwt.decorators.user_passes_test

As a shortcut, you can use the convenient ``user_passes_test()`` decorator which raises a ``PermissionDenied`` exception when the callable returns ``False``

【 可以使用 ``user_passes_test()`` 传入回调函数判定访问权限，若返回 False 则会抛出 ``PermissionDenied`` 异常  】

::

    from django.contrib.auth import get_user_model

    import graphene
    from graphql_jwt.decorators import user_passes_test


    class Query(graphene.ObjectType):
        users = graphene.List(UserType)

        @user_passes_test(lambda user: user.email.contains('@staff'))
        def resolve_users(self, info, **kwargs):
            return get_user_model().objects.all()

``user_passes_test()`` takes a required argument: a callable that takes a ``User`` object and returns ``True`` if the user is allowed to perform the action. Note that ``user_passes_test()`` does not automatically check that the ``User`` is not anonymous.

【 ``user_passes_test()`` 装饰器有一个必填参数：接受 ``user`` 对象并返回布尔值的函数，若允许该用户执行则需返回 True。 请注意 ``user_passes_test()`` 并不会检查 ``user`` 是否匿名用户 】

@permission_required
--------------------

  .. autofunction:: graphql_jwt.decorators.permission_required

Decorator to check whether a user has a particular permission.

【 该装饰器检查用户是否具有特定权限。 】

Just like the ``has_perm()`` method, permission names take the form

【 就像 ``has_perm()`` 方法一样，权限名称采用以下方式 】

::

    <app-label>.<permission-codename>

The decorator may also take an iterable of permissions, in which case the user must have all of the permissions in order to access the resolver or mutation

【 装饰器还可以获取可迭代的权限，在这种情况下，用户必须具有所有权限才能访问 *resolver* 或 *mutation* 】

::

    import graphene
    from graphql_jwt.decorators import permission_required

    class UpdateUser(graphene.Mutation):

        class Arguments:
            user_id = graphene.Int()

        @classmethod
        @permission_required('auth.change_user')
        def mutate(cls, root, info, user_id):
            ...


@staff_member_required
----------------------

  .. autodata:: graphql_jwt.decorators.staff_member_required

A resolver or mutation decorated with this function will having the following behavior:

【 使用这个装饰器的 *resolver* 和 *mutation* 具有以下行为： 】

If the user is logged in, is a staff member (``User.is_staff=True``), and is active (``User.is_active=True``), execute the function normally.

Otherwise, the ``PermissionDenied`` exception will be raised

【 若用户已登录，``User.is_staff=True`` 和 ``User.is_active=True`` 则正常执行函数。否则抛出 ``PermissionDenied`` 异常 】

::

    from django.contrib.auth import get_user_model

    import graphene
    from graphql_jwt.decorators import staff_member_required


    class Query(graphene.ObjectType):
        users = graphene.List(UserType)

        @staff_member_required
        def resolve_users(self, info, **kwargs):
            return get_user_model().objects.all()


@superuser_required
-------------------

  .. autodata:: graphql_jwt.decorators.superuser_required

A resolver or mutation decorated with this function will having the following behavior:

【 使用这个装饰器的 *resolver* 和 *mutation* 具有以下行为： 】

If the user is active (``User.is_active=True``) and is superuser (``User.is_superuser=True``), execute the function normally.

Otherwise, the ``PermissionDenied`` exception will be raised

【 若用户已登录，``User.is_active=True`` 和 ``User.is_superuser=True`` 则正常执行函数。否则抛出 ``PermissionDenied`` 异常 】

::

    import graphene
    from graphql_jwt.decorators import superuser_required


    class DeleteUser(graphene.Mutation):

        class Arguments:
            user_id = graphene.Int()

        @classmethod
        @superuser_required
        def mutate(cls, root, info, user_id):
            ...
