Services are different components of Cruddy's core that might be extended in some
way. Each service is available from the IoC (i.e. `app('cruddy')` is the main service,
`app('cruddy.assets')` is a component responsible for assets management).

-   [Fields & Columns](#field-columns)
-   [Assets](#assets)
-   [Lang](#lang)

## Fields & Columns

`cruddy.fields` and `cruddy.columns`

Both fields and columns services are extendable factories. To register a new field
or column type do following:

```php
$columns = app('cruddy.columns');

$columns->register('foo', 'Class\Name');
```

Where `foo` is the type name and `Class\Name` is the name of the corresponding class
that should be resolvable through the IoC.

The class name can also be a closure:

```php
$fields = app('cruddy.fields');

$fields->register('bar', function ($entity, $collection, $id, $extra)
{
    $field = new My\Field($entity, $id, $extra);

    $collection->add($field);

    // Return the field to allow additional configuration
    return $field;
});
```

Now we can define a field of type `bar`:

```php
$schema->bar('test', $extra);
```

Every parameter that are passed to `bar` function will be passed to the closure
along with `$entity` and `$collection`.

Remember that field type should implement `Kalnoy\Cruddy\Schema\Fields\FieldInterface`
and column should implement `Kalnoy\Cruddy\Schema\Columns\ColumnInterface`. But usually
you'll extend some base classes, feel free to investigate the source.

## Assets

`cruddy.assets`

## Lang

`cruddy.lang`