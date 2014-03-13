Field is an entity component. Field extracts data and sends it to the UI, where user can edit it. After he saves a data, field will process it to send to the [[repository|Repository]].

Fields are defined in entity's [[schema|Schema]].

* [Basic types](#wiki-basic-types)
* [Relational types](#wiki-relational-types)

## Common operations

Each field is an object, which can be modified.

### Labels & help

[[Localization]]

### Required fields

You can mark field as required:

```php
$schema->string('title')->required();
```

Required field is marked with red star to give user a clue, but no validation is performed.

### Unique fields

In Cruddy you can copy models. But sometimes it is not desired that field is copied (like timestamps). You can disable copying specific field using `unique` modifier:

```php
$schema->datetime('last_login_at')->unique();
```

_Some fields are unique by default, like `$schema->timestamps()` and `$schema->increments()`._

### Disabling fields

You can disable field editing using `disable` modifier:

```php
$schema->boolean('active')->disable();
```

Such field will just display a value without possibility to edit it.

_If corresponding attribute of an Eloquent model is not fillable, field is disabled by default._

### Labels

Field label is generated from the id. For example, the field with an id of `first_name` will have label of `First name`. You can set label manually:

```php
$schema->string('first_name')->label('Name');
```

Read more about how to set field label in [[localization]] page.

## Field types

All fields can be devided into two big groups: _basic fields_ and _relational fields_. Basic fields represent such simple types like strings, booleans, texts and images. Relational fields are for joining several models together.

### Basic types

* [String](#wiki-string)
* [Email](#wiki-email)
* [Text](#wiki-text)
* [Code](#wiki-code)
* [Markdown](#wiki-markdown)
* [Enum](#wiki-enum)
* [Boolean](#wiki-boolean)
* [Password](#wiki-password)
* [Datetime](#wiki-datetime)
* [Date](#wiki-date)
* [Time](#wiki-time)
* [Timestamps](#wiki-timestamps)
* [File](#wiki-file)
* [Image](#wiki-image)

#### String

String field is a simple `<input>`.

```php
$field = $schema->string('phone');

// Set a mask http://digitalbush.com/projects/masked-input-plugin
$field->mask('999-9999');

// Set a placeholder
$field->placeholder('Your phone');
```

#### Email

```php
$schema->email('email');
```

#### Text

Simple text editing input, represented by `<textarea>`.

```php
$schema->text('bio')->rows(5);
```

#### Code

This field will display a code editor ([Ace](ace.c9.io)) with specified mode, theme and height.

```php
$schema->code('code')->theme('monokai')->mode('js')->height(150);
```

#### Markdown

A code editor with possibility to view parsed text.

```php
$schema->markdown('text');
```

#### Enum

This field represents a combo box or `<select>` input.

```php
$schema->enum('type', [ 'first' => 'First type', 'second' => 'Second type' ]);
```

It is possible to pass a closure, that returns items:

```php
$schema->enum('type', function () { return $items; });
```

You can specify a prompt item:

```php
$schema->enum('type', [...])->prompt('Please, select a type.');
```

#### Boolean

```php
$schema->boolean('active');
```

#### Password

Password field doesn't extract any data and always returns empty value. It will pass no value to the repository if user did not enter any value.

```php
$schema->password('password');
```

#### Datetime

```php
$schema->datetime('deleted_at');
```

Each date-related field has a format which can be changed:

```php
$schema->datetime('deleted_at')->format('HH:mm');
```

See [available formats](http://momentjs.com/docs/#/displaying/format/).

#### Date

```php
$schema->date('date');
```

#### Time

```php
$schema->time('time');
```

#### Timestamps

Shortcut for adding `updated_at` and `created_at` fields.

```php
$schema->timestamps();
```

#### File

```php
$field = $schema->file('files');

// Several files can be uploaded
$field->many();

// Specify a mime type for an `<input>`.
$field->accepts('text/plain');
```

Read more about [[how files are uploaded|repository]].

#### Image

Image is also a file, but image will be displayed as image rather than a filename. Also, `accepts` modifier is already set to `image/*`.

```php
$schema->image('image');
```

### Relational types

Relational fields depend on relations rather than attributes. They take all needed information from relational queries that are defined on model.

* [Relates](#wiki-relates)
* [Embedded](#wiki-inline)

#### Relates

This field type is for connecting models between. In the UI you will see a dropdown with possibility to search and select item or items.

Following relations are supported: `BelongsTo`, `BelongsToMany`, `HasMany`, `MorphToMany`.

```php
// Second argument is an identifier of related entity
$schema->relates('group', 'groups');
```

##### Filtering options

It is possible to filter available options:

```php
$schema->relates('group', 'groups')->filterOptions(function ($q)
{
    $q->where('status', '=', 1);
}
```

##### Constraining relations

You can constraint available options based on other field. For example, when you select specific country, you get states only of that country.

```php
$schema->relates('country', 'countries');
$schema->relates('state', 'states')->constraintWith('country');
```

[[Read more...|Filtering relation options]]

#### Embedded

This is most interesting feature of Cruddy. Embedded fields allow to edit related models _inside_ current. Imagine that you have a `Post` and `MetaData`. Meta data is stored in other table and `Post` `hasOne` `MetaData`. It is possible to edit post __and__ metadata together and not in separate forms. Another example is a product that has many parameters. You can add as many parameters as you want while editing product.

Following relations are supported: `HasOne`, `MorphOne`, `HasMany`, `MorphMany`.

```php
$schema->embed('meta', 'meta');
```

_Just like for [relates](#wiki-relates) fields you need to specify related entity's identifier._

See [[this tutorial|Tutorial:-embedding-forms]] for more info.