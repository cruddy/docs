Column is responsible for displaying a data in the data grid. It also orders data.

Columns are defined under `columns` method in entity's [[schema]].

## Defining columns

You will most likely use one type of columns -- `Proxy`. This type of column just
references a field to extract and format data. This column is defined this way:

```php
$s->col('name');
```

Now you will have a column that will display `name` field's value.

Sometimes you need to display not ordinary data. Computed columns come in handy.
You can specify a closure for extracting data:

```php
$s->compute('full_name', function ($person)
{
    return $person->first_name.' '.$person->last_name;
});
```

You can specify a clause to allow ordering:

```php
$s->compute('full_name', ...)->clause(DB::raw('concat(first_name, last_name)'));
```

## Default configuration

### Setting formatter

By default, column is formatted using a field that the column refers to, but there
are cases when you need to override this behaviour. Formatter is a JavaScript class
under `Cruddy.Formatters` namespace that is responsible for converting column's value
to the string representation.

```php
$s->col('avatar')->format('Image', [ 'width' => 150 ]);
```

#### Supported formatters

##### Plain

Do disable formatting at all, you can use `Plain` formatter:

```php
$s->col('foo')->format('Plain');
```

#### Image

`Image` formatter inserts a thumbnail into data grid:

```php
$s->col('image')->format('Image', $options);
```

Where `$options` is an array of following options:

- `width` -- maximum width of the thumbnail
- `height` -- maximum height of the thumbnail

Options are completely optional.

### Default order direction

The default order direction is ascending. You can reverse it:

```php
$s->col('name')->reversed();
```

### Changing header

[Localizing columns](localization#changing-columns-header).