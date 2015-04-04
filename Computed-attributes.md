Sometimes you need to just display some value that is not actually stored in database. You can use computed field or
column for this:

```php
$schema->compute('total', function ($model) { return $model->getTotal(); });
```

You can also specify a method on model class, so previous example can be implemented even so:

```php
$schema->compute('total', 'getTotal');
```

Actually, Cruddy will guess that you need function `getTotal` for `total` attribute, so this can be further shorten to this:

```php
$schema->compute('total');
```

**Relations**

If computed value relies on some related models, you can specify which relations should be eagerly loaded to avoid 
`n+1` problem:

```php
$schema->compute('bar')->eager([ 'bar', 'bar' ]);
```