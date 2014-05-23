Cruddy was initially designed to be highly extensible, since not a single developer
will be satisfied with the default set of fields and columns.

You can create your own field and column types from base Cruddy types or create
completely new ones based on interfaces.

## Creating new field type

Creating a field type is not an easy task. There is [[an article|plugins/new-field-type]]
dedicated to this process.

## Registering new css and js files

It's very likely that you will have to add custom CSS and JS files.

To add a CSS file to the main layout, use this function:

```php
Cruddy::css(asset('css/foo.css'));
```

To add a JS file:

```php
Cruddy::js(asset('js/bar.js'));
```