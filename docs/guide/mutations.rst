==============================
Mutations
==============================

DjangoCreateMutation
----------------------

Mutation class for creating a new instance of the supplied model.

The mutation accepts one argument named `input`. The mutation returns a single field for resolving,
which is the camel-case version of the model name.

.. code:: python

    class CreateUserMutation(DjangoCreateMutation):
        class Meta:
            model = User


.. code::

    mutation {
        createUser(input: {name: "John Doe", address: "161 Lexington Avenue"}){
            user{
                id
                name
                address
            }
        }
    }


DjangoUpdateMutation
----------------------

Mutation class for updating an existing instance of the supplied model.

The mutation accepts two arguments named `id`, and `input`. The mutation returns a single field for resolving,
which is the camel-case version of the model name.

The type of the `id` argument is `ID`. However, both regular primary keys and relay global id's are accepted and
handled properly.

By default, all :ref:`included fields<included_and_excluded>` of the model are marked as required in the input.

.. code:: python

    class UpdateUserMutation(DjangoUpdateMutation):
        class Meta:
            model = User


.. code::

    mutation {
        updateUser(id: "VXNlck5vZGU6MQ==", input: {name: "John Doe", address: "161 Lexington Avenue"}){
            user{
                id
                name
                address
            }
        }
    }


DjangoPatchMutation
----------------------

Mutation class for updating an existing instance of the supplied model.

The mutation accepts two arguments named `id`, and `input`. The mutation returns a single field for resolving,
which is the camel-case version of the model name.

The type of the `id` argument is `ID`. However, both regular primary keys and relay global id's are accepted and
handled properly.

All fields of the model are marked as **not required**.

.. code:: python

    class PatchUserMutation(DjangoPatchMutation):
        class Meta:
            model = User


.. code::

    mutation {
        patchUser(id: "VXNlck5vZGU6MQ==", input: {name: "John Doe"}){
            user{
                id
                name
                address
            }
        }
    }


DjangoDeleteMutation
----------------------

Mutation class for deleting a single instance of the supplied model.

The mutation accepts one argument named `id`. The type of the `id` argument is `ID`. However, both regular primary keys and relay global id's are accepted and
handled properly.

The mutation returns two fields for resolving:

- ``found``: True if the instance was found and deleted.
- ``deletedId``: The id (primary key) of the deleted instance.

.. code:: python

    class DeleteUserMutation(DjangoDeleteMutation):
        class Meta:
            model = User


.. code::

    mutation {
        deleteUser(id: "VXNlck5vZGU6MTMzNw=="){
            found
            deletedId
        }
    }


DjangoBatchCreateMutation
--------------------------

Mutation class for creating multiple new instances of the supplied model.

The mutation accepts one argument named `input`, which is an array-version of the typical create-input. The mutation returns a single field for resolving,
which is the camel-case version of the model name.

.. code:: python

    class BatchCreateUserMutation(DjangoBatchCreateMutation):
        class Meta:
            model = User


.. code::

    mutation {
        batchCreateUser(input: {name: "John Doe", address: "161 Lexington Avenue"}){
            user{
                id
                name
                address
            }
        }
    }

DjangoBatchUpdateMutation
--------------------------

Mutation class for update multiple instances of the supplied model.

The mutation accepts one argument named `input`, which is an array-version of the typical update-input, with the addition that all object IDs are inside the objects. The mutation returns a single field for resolving,
which is the camel-case version of the model name.

.. code:: python

    class BatchUpdateUserMutation(DjangoBatchUpdateMutation):
        class Meta:
            model = User


.. code::

    mutation {
        batchUpdateUser(input: [{
            id: "VXNlck5vZGU6MTMzNw==",
            name: "John Doe",
            address: "161 Lexington Avenue"
        }]){
            user{
                id
                name
                address
            }
        }
    }


DjangoBatchPatchMutation
--------------------------

Mutation class for patching multiple instances of the supplied model.

The mutation accepts one argument named `input`, which is an array-version of the typical update-input, with the addition that all object IDs are inside the objects. The mutation returns a single field for resolving,
which is the camel-case version of the model name.

.. code:: python

    class BatchPatchUserMutation(DjangoBatchPatchMutation):
        class Meta:
            model = User


.. code::

    mutation {
        batchPatchUser(input: [{
            id: "VXNlck5vZGU6MTMzNw==",
            address: "161 Lexington Avenue"
        }]){
            user{
                id
                name
                address
            }
        }
    }


DjangoFilterUpdateMutation
--------------------------

Mutation class for updating multiple instances of the supplied model. The filtering used to decide which
instances to update, is defined in the meta-attribute `filter_fields`.

The mutation accepts two arguments named `filter` and `data`. The shape of `filter` is based on the contents of `filter_fields`.
The fields, and their input, is passed directly to an `Model.objects.filter`-call.

The shape of `data` is similar to a DjangoUpdateMutation `input` field, although all fields are optional by default.

The mutation returns two fields for resolving:

- ``updatedCount``: The number of updated objects.
- ``updatedObjects``: The updated objects.

.. code:: python

    class FilterUpdateUserMutation(DjangoFilterUpdateMutation):
        class Meta:
            model = User
            filter_fields = (
                "name",
                "house__address",
                "house__owner__name__in"
            )


.. code::

    mutation {
        filterUpdateUsers(
            filter: {
                "name": "John Doe",
                "house_Owner_Name_In": ["Michael Bloomberg", "Steve Jobs"]
            },
            data: {
                "name": "New name"
            }
        ){
            updatedObjects{
                id
                name
            }
        }
    }


DjangoFilterDeleteMutation
--------------------------

Mutation class for deleting multiple instances of the supplied model. The filtering used to decide which
instances to delete, is defined in the meta-attribute `filter_fields`.

The mutation accepts one argument named `input`. The shape of `input` is based on the contents of `filter_fields`.
The fields, and their input, is passed directly to an `Model.objects.filter`-call.

The mutation returns two fields for resolving:

- ``deletionCount``: True if the instance was found and deleted.
- ``deletedIds``: The id (primary key) of the deleted instance.

.. code:: python

    class BatchDeleteUserMutation(DjangoBatchDeleteMutation):
        class Meta:
            model = User
            filter_fields = (
                "name",
                "house__address",
                "house__owner__name__in"
            )


.. code::

    mutation {
        batchDeleteUser(input: {"name": "John Doe", "house_Owner_Name_In": ["Michael Bloomberg", "Steve Jobs"]}){
            user{
                id
                name
                address
            }
        }
    }


DjangoBatchDeleteMutation
--------------------------

Mutation class for deleting multiple instances of the supplied model.

The mutation accepts one argument named `ids`, which is an array of object IDs.

The mutation returns two fields for resolving:

- ``deletionCount``: The number of deleted instances.
- ``deletedIds``: The id (primary key) of the deleted instance.
- ``missedIds``: The id (primary key) of the instances not found.

.. code:: python

    class BatchDeleteUserMutation(DjangoBatchDeleteMutation):
        class Meta:
            model = User


.. code::

    mutation {
        batchDeleteUser(ids: [
            "VXNlck5vZGU6MTMzNw=="
        ]){
            user{
                id
                name
                address
            }
        }
    }

