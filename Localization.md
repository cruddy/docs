Before you continue, please read about [localization](http://laravel.com/docs/localization) in Laravel.

## Localizing entities

Each entity can have separate language file under `app/lang` directory. For example, to localize `users` entity, you need to create `app/lang/en/users.php` file. This file can have following sections:

* `title` to define entity's title in plural and singular form
* `help` to specify help for fields and columns
* `fields` to alter field labels
* `columns` to alter column headers

There is also a master file with a name of `entities.php` that can have `help`, `fields` and `columns` sections where you can specify values that will be used by all entities. This is the place for defining common labels and headers.

### Changing field label

To specify a field label, you need to define it under `fields` section. I.e. for `updated_at` field:

```php
'fields' =>
[
    'updated_at' => 'Updated at',
],
```

That's how the label of the field with an id of `{id}` of the entity with an id of `{entity}` is resolved:

1. Cruddy tries to translate a key of `{entity}.fields.{id}`,
2. if no value is found, it tries other key: `entities.fields.{id}`;
3. Otherwise, Cruddy just prettifies field's id.

### Changing column's header

The rules are the same as for changing field's label, but header is specified under `columns` section.

### Specifying help message

You can specify a help message for a field or column under `help` section of entity's language file:

```php
'help' =>
[
    'updated_at' => 'The last time when resource was updated',
],
```

### Changing entity's title

To change entity's title, you need to specify singular and plural forms, like so:

```php
'title' => [ 'singular' => 'User', 'plural' => 'Users' ],
```