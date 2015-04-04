When the number of fields and embedded entities is growing, the form becomes pretty
messy and really tall. In order to make the form more pretty, you can define a layout
based on tabs, fieldsets, rows and columns.

Here's the example of the layout:


```php
protected function layout($l)
{
    $l->row([ 'first_name', 'last_name' ]);
    $l->fieldset('Credentials', [ 'email', 'password' ]);

    $l->tab('Profile', 'profile');
}
```

__IMPORTANT:__ _when defining the layout, fields that aren't referenced will not be displayed._

So, the layout consists of several elements:

- __field__ is a basic element that contains a field
- __row__ is a set of columns of specified width
- __col__ or _column_ is a part of a row that can contain other rows and fields
- __fieldset__ is a named set of rows and fields
- __tab__ or _tab pane_ is a separate tab that can contain fieldsets, rows and fields

The only element that can be appended to the form layout is `tab`. But defining other elements
for it will be passed to the _default_ pane.

### Defining fields

The field is a basic element which can be included into other element:

```php
$e->field('foo');
$e->field('baz', 'bar');
```

### Rows

Row consists of columns of some width measured in bootstrap's spans. The sum of spans must be
a total of 12.

Define a row of columns of equal widths:

```php
// Both fields will take 6 spans
$e->row([ 'first_name', 'last_name' ]);
```

Specifying width:

```php
// The field `foo` will have a width of 2 spans, `bar` will get rest spans (10)
$e->row([ [ 2, 'foo' ], 'bar' ]);
```

Columns with more than one field:

```php
$e->row([ [ 'first_name', 'last_name' ], [ 'email', 'password' ] ]);
```

More control using a closure:

```php
$e->row(function ($r)
{
    $r->col(4, 'first_name');
    $r->col(4, 'last_name');
    
    // Define a columns which width will be calculated
    $r->field('is_active');
});
```

### Fieldset

Fieldset is a named set of rows and fields. Basic example:

```php
$e->fieldset('Title', $element);
```

The title can also be a language line reference like `users.credentials`.

The `$element` there can be a closure, an array or string. Inside a closure you can
define rows and fields explicitly:

```php
$e->fieldset('Title', function ($f)
{
    $f->row(...);
    $f->field(...);
});
```

Simple string is a reference to the field. Most interesting is when `$element` is an array.
Each element of the array can be a string or another array. The string means a field,
an array means row definition.

```php
$e->fieldset('Title', [
    // a row
    [ 'first_name', 'last_name', [ 2, 'is_active' ] ],
    
    // fields
    'email', 'password',
]);
```

Here is the equivalent:

```php
$e->fieldset('Title', function ($f)
{
    $f->row(function ($r)
    {
        $r->field('first_name', 'last_name');
        $r->col(2, 'is_active');
    });
    
    $f->field('email', 'password');
});
```

Much more verbose, right?

### Tab panes

Tab pane is basically the same as a fieldset, but fields will be displayed in a separate tab.

```php
$e->tab('Title', ...);
```