Before you continue, please read about [localization](http://laravel.com/docs/localization) in Laravel.

Cruddy can automatically translate entity's title, field, column and filter labels, and also automatically add a help
message.

In order to do this, you need to create `entities.php` file under a language-specific folder, i.e. `lang/ru/entities
.php`. This file has following sections:

-   `titles` for translating entities titles;
-   `fields` for translating field labels;
-   `columns` for translating column headers;
-   `filters` for translating filter titles;
-   `help` for applying help message.

### Setting entity's title

The title of the entity with an id of `foo` can be clarified easily:

```php
'titles' => [
    'foo' => [ 'singular' => 'Foo', 'plural' => 'Foos' ],
],
```

Title is defined in singular and plural forms.

### Changing labels

In order to change a label for filter, column or filter, you need to add an entry in corresponding section. For example,
imagine we have field with an id of `foo`, it can be translated by adding following entry under `fileds` section:

```php
'fields' => [
    'foo' => 'Bar',
],
```

### Specifying help message

Each field and column can get a help message automatically from localization file:

```php
'help' => [
    'foo' => 'Foo help message',
],
```

In this case, each field and column with an id of `foo` will have a help message.

## Overriding common labels

Sometimes happens that some common field of an entity needs to be labeled differently. In this case, you can create
a separate language file with a name of the id of the entity, i.e. `products.php`. This file can contain same sections
as `entities.php`.

## Localizing menu items

Each menu item's label and a dropdown title can be a translation key. Cruddy will automatically translate it.