Entity's schema is a main component that you will work with. In a schema you define
what fields and columns the model has, the validation rules and other. You can
provide your own repository and validator, your own main entity object. In a word,
you can control look'n'feel of your entity.

- [Generating schema](#generating-schema)
- [Base configuration](#base-configuration)
- [Tuning](#tuning)
- [More control](#more-control)

## Generating schema

Cruddy is shipped with command for generating basic schema:

```
php artisan cruddy:schema FooSchema --model Foo
```

You will get generated `FooSchema` in `app/entities` directory.

### Changing directory

You can generate schemas into different folder:

```php
php artisan cruddy:schema FooSchema --path app/Acme/Entities
```

### Setting namespace

You can provide a namespace for schema:

```php
php artisan cruddy:schema FooSchema --namespace Acme\Entities
```

## Base configuration

### Specifying fields

Fields are set up in `fields` method:

```php
public function fields($s)
{
    $s->primary('id');
    $s->string('name');
}
```

See the list of [[available fields|fields]].

### Specifying columns

Columns are defined in `columns` method:

```php
public function columns($s)
{
    $s->col('id');
    $s->col('name');
}
```

See the list of [[available columns|columns]].

### Validation rules

[[Main article|validation]]

### Specifying file uploads

[[Main article|repository]]

## Tuning

### Setting title attribute property

Sometimes there is need to convert a model to a string. Each model is converted
to a string before you see it in a drop down list. By default, Cruddy uses model's
key to represent a model. You can set other attribute that will be used:

```php
protected $titleAttribute = 'name';
```
You can also override `toString` method to get more control:

```php
public function toString($product)
{
    return $product->name.' ('.$product->sku.')';
}
```

### Setting default order

You can provide a name of the column which is used to order models by default:

```php
protected $defaultOrder = 'name';
```

### Providing default attributes

In order model to have some predifined attributes, use this property:

```php
protected $defaults = [ 'is_active' => true ];
```

Or you can override `defaults` method to get more control:

```php
protected function defaults()
{
    return [ 'is_active' => true ];
}
```

### Advanced filters

Besides search box that is available for every entity, you can add more complex
filters:

```php
protected $filters = [ 'groups' ];
```

Each item in the array is an identifier of the field. You can filter by related
models, numbers and booleans.

### Overriding default per page value

If you need to override default per page value that is acquired from a model:

```php
protected $perPage = 50;
```

### Providing external url

If you want to have a link to the model on main site in backend, you can override
special method:

```php
protected function externalUrl($model)
{
    return route('post.show', [ $model->getKey() ]);
}
```

### Custom states for rows

You can apply custom CSS classes or _states_ per row in the datagrid. You
just need to define a meta column `states` like so:

```php
public function columns($schema)
{
    ...

    $schema->states(function ($model)
    {
        return $model->isNew() : 'new' : '';    
    });
}
```

Or this way:

```php
$schema->states(
[
    // The model will have a state of "new" if closure returns true
    'new' => function ($model) { return $model->isNew(); },
]);

// The equivalent of previous
$schema->states([ 'new' ]);
```

Since Cruddy is based on Twitter Bootstrap, the datagrid has following
[predefined states](http://getbootstrap.com/css/#tables-contextual-classes): 
`success` (green), `info` (blue), `warning` (yellow), `danger` (red).

```php
$schema->states(
[
    'success' => function ($model) { return $model->isNew(); },
]);
```

## More control

- [Custom validator](validation#custom-validator)