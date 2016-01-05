Field extracts data for the UI, where user can edit it. After he saves the data, field will process it to send to the 
[[repository|Repository]].

Fields are defined in the [[schema|Schema]].

* [Basic types](#basic-types)
* [Relational types](#relational-types)

## Common operations

Each field is an object, which can be modified.

### Labels & help

Field label is generated automatically from the id.

You can override the label like so:

```php
$schema->string('foo')->label('bar');
```

To set the help message:

```php
$schema->string('foo')->help('bar');
```

Also, you can set up common labels and help using [[language files|localization]].

### Required fields

__IMPORTANT!__ Since v0.4.0 fields are automatically marked required based on the information from the validation 
rules.

You can mark field as required:

```php
$schema->string('title')->required();
```

Required field is marked with red star to give user a clue, but __no validation is performed__.

The field can be marked required only when model is exists or when it is new:

```php
$schema->string('foo')->required(self::WHEN_NEW);
$schema->string('bar')->required(self::WHEN_EXISTS);
```

### Unique fields

In Cruddy you can copy models. But sometimes it is not desired that field is copied (like timestamps).
You can disable copying specific field using `unique` modifier:

```php
$schema->datetime('last_login_at')->unique();
```

_Some fields are unique by default, like `$schema->timestamps()` and `$schema->increments()`._

### Disabling fields

You can disable a field using `disable` modifier:

```php
$schema->boolean('active')->disable();
```

Such field will just display a value without possibility to edit it.

You can disable a field only when model is new:

```php
$schema->float('price')->disable(self::WHEN_NEW);
```

Or when model is exists:

```php
$shema->integer('type')->disable(self::WHEN_EXISTS);
```

### Input addons

For input-like field (string, email, password, integer, float) it is possible to
[append or prepend](http://getbootstrap.com/components/#input-groups-basic) some text:

```php
$schema->float('price')->append('$');

// Those labels are automatically translated
$schema->string('foo')->prepend('bar.baz');
```

## Field types

All fields can be devided into two big groups: _basic fields_ and _relational fields_.
Basic fields represent such simple types like strings, booleans, texts and images.
Relational fields are for joining several models together.

### Basic types

String types             | Date types                 | File types         | Other types                      
------------------------ | -------------------------- | ------------------ | ---------------------------------
[String](#string)        | [Datetime](#datetime)      | [File](#file)      | [Number formats](#number-formats)
[Email](#email)          | [Date](#date)              | [Image](#image)    | [Enum](#enum)                    
[Password](#password)    | [Time](#time)              |                    | [Boolean](#boolean)              
[Text](#text)            | [Timestamps](#timestamps)  |                    | [Computed](#computed)    
[Slug](#slug)            |                            |                    |

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

#### Slug

Slug is a text field that applies `str_slug` on its data before validating and
saving the model. Also, it can copy value from target field:

```php
$schema->slug('slug', 'title');
```

Now field `slug` copies value from `title` field when creating new instance.

You can specify which separator to use:

```php
$schema->slug('slug', 'title')->separator('_');
```

#### Number formats

You can define an integer and a float types:

```php
$schema->integer('number');
$schema->float('price');
```

Numbers are simple text fields that provide special filter.

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

Enum can become a set (i.e. multiple values accepted):

```php
$schema->enum('role', [...])->multiple();
```

Make sure, that `role` field is casted to array in your model.

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
$schema->timestamps($hide = false, $disable = null);
```

By default, `updated_at` field is disabled and `created_at` isn't. To
disable both fields set `$disable` parameter to `true`:

```php
$schema->timestamps(false, true);
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

[Handling multiple file uploads](repository#multiple-file-uploads).

#### Image

Image is also a file, but image will be displayed as image rather than a filename. Also, `accepts` modifier is already set to `image/*`.

```php
$schema->image('image');
```

To set the thumbnail size:

```php
$schema->image('foo')->thumbnailSize(150, 150);

// Height will be computed based on aspect ratio
$schema->image('bar')->width(150);

// Width will be computed based on aspect ratio
$schema->image('baz')->height(100);
```

#### Computed

You can display some computed value that will not be editable.

[[Read more|computed attributes]]

### Relational types

Relational fields depend on relations rather than attributes. So, in order to define relational field, corresponding relation must be defined on target Eloquent model.

* [Dropdown selector](#dropdown-selector)
* [Embedding forms](#embedding-forms)

#### Dropdown selector

This field type is for connecting models between. In the UI you will see a dropdown that is able to search and select item or items.

Following relations are supported: `BelongsTo`, `BelongsToMany`, `MorphToMany`.

```php
// Second argument is an identifier of related entity
$schema->relates('group', 'groups');
```

In this case a `group` relation is required on target model.

__IMPORTANT!__ Read [this](schema#setting-title-attribute-property) if you're wondering how to display sensible names in
a list.

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

#### Embedding forms

This is most interesting feature of Cruddy. Embedded forms allow to edit related models _inside_ current. Imagine that you have a `Post` and `MetaData`. Meta data is stored in other table and `Post` `hasOne` `MetaData`. It is possible to edit post __and__ metadata together and not in separate forms. Another example is a product that has many parameters. You can add as many parameters as you want while editing product.

Following relations are supported: `HasOne`, `MorphOne`, `HasMany`, `MorphMany`.

```php
$schema->embed('meta', 'meta');
```

_Just like for [dropdowns](#dropdown-selector) you need to specify related entity's identifier._

See [[this tutorial|embedding-forms]] for more info.

##### Validating embedded models

Actual data of related model is validated using rules that are defined in related entity, but the field is available when validating target model. This field will contain a number of created models, so you can check whether user provided related model:

```php
'meta' => 'required',
```