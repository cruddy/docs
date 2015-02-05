In this tutorial you'll learn how to install Cruddy on top of your Laravel app. If you don't have one, please follow [this guide](http://laravel.com/docs/installation). I also assume that you are familiar with the Laravel and know what is Eloquent model and how it works.

* [Installation](#wiki-installation)
* [First entity](#wiki-first-entity)
    * [Eloquent model](#wiki-eloquent-model)
    * [The schema](#wiki-the-schema)
        * [Fields](#wiki-fields)
        * [Columns](#wiki-columns)
        * [Default value for fields](#default-values-for-fields)
        * [Complex filters](#wiki-complex-filters)
        * [Image](#wiki-image)
        * [Validation](#wiki-validation)
    * [Making it visible](#wiki-making-it-visible)
    * [Dashboard](#wiki-dashboard)
* [Further reading](#wiki-further-reading)

## Installation

Cruddy is available as a composer package. To install it, run following command in the terminal (make shure that current directory is your app's root):

```
composer require kalnoy/cruddy:~0.5.0
```

Then you need to publish some assets and config file:

```
php artisan vendor:publish
```

_You might want to publish assets when composer is updated since Cruddy assets are updated frequently. See [composer scripts](https://getcomposer.org/doc/articles/scripts.md)._

The next step is to enable Cruddy by adding a service provider:

```php
'providers' => array(
    'Kalnoy\Cruddy\CruddyServiceProvider',
),
```

You can also specify a facade to quickly access [[the environment]]:

```php
'facades' => array(
    'Cruddy' => 'Kalnoy\Cruddy\Facades\Cruddy',
),
```

That's it! You can access backend page, though it is empty for now. By default, dashboard is available under `backend` route, but it is configurable.  So, the page address would look like something like this:

```
http://cruddy-demo.dev/backend
```

It's time to add some entity.

## First entity

Cruddy Entity is a mediator between [Eloquent models](http://laravel.com/docs/eloquent) and UI. It handles all CRUD operations. [[Read more about entities|the-entity]].

At this moment, we are interested in entity's schema, that describes fields, columns and validation rules. But we don't have any model to work with, so let's create one.

### Eloquent model

_You can skip this section if you already have some eloquent models._ 

We will create a simple post model, that will have a title, a body, an image and a boolean flag that determines whether the post is active. This is how table's [blueprint](http://laravel.com/docs/migrations) looks like:

```php
$table->increments('id');
$table->string('title');
$table->string('image');
$table->text('body');
$table->boolean('active');
$table->timestamps();
```

Pretty common schema, right? Here is the model class:

```php
// app/Post.php
<?php

/**
 * Model Post
 */
class Post extends Eloquent {

    protected $table = 'posts';

}
```

### The schema

Cruddy has a command for generating basic schema. To do this, simply execute following:

```
php artisan make:entity Post
```

New schema will be created in `app/Entities` directory.

To add schema to the repository, you need to open cruddy configuration file (`app/config/packages/kalnoy/cruddy/config.php`) and add a line to `entities` section, like so:

```php
'entities' =>
[
    'posts' => 'App\Entities\Post',
],
```

Now you have registered entity with name of `posts` and you can browse it right now going at `http://cruddy-demo.dev/backend/posts`. You'll see a table with two columns and nothing actually editable... That's because we haven't yet defined any fields nor columns.

_Each schema component is defined in separate function. I will provide full component definition along with function name so you can orient._

#### Fields

Field is an input that you are able to edit in the form. Let's add some under `fields` function in schema definition:

```php
public function fields($schema)
{
    $schema->increments('id');
    
    $schema->string('title')->required();

    // Use ACE to edit body as markdown
    $schema->markdown('body')->required();

    $schema->boolean('active');

    $schema->timestamps();
}
```

Fields definition is much like Laravel's Blueprint that we use to define table columns. Here we have added three fields
besides auto generated ones: `title`, `body`, and `active`. They map to actual table columns and are of basic data type.
Then, you'll learn about more complex data types like relations and inline forms.

Have you noticed `required` modifier? It just tels the UI to display additional note that field is required, it doesn't
do any validation!

Most of fields return actual field object which can be altered with some modifiers, but some are just macros, like 
`timestamps`. This macros adds two fields: `updated_at` and `created_at`. You can define your own macros.

[[See the list of available fields|Fields]].

#### Columns

Column will display some value in the list of items. It just extracts data from a model and displays it in special
format. They are also responsible for sorting data, but not all columns can do that.

Here is the columns for `Post` model:

```php
public function columns($schema)
{
    $schema->col('id');
    $schema->col('title');
    $schema->col('active');
    $schema->col('updated_at')->orderDirection('desc');
}
```

We just reference fields here. This is the most common use case.

[[Read more about columns|Columns]].

#### Default values for fields

We can set default values for fields in creation form. For that we need add property ```$defaults``` array, where key
is field's id, and value - field's value. For example:

```php
protected $defaults = ['free' => true, 'booked' => true, 'number' => '2'];
```

#### Complex filters

By default, Cruddy adds a search box for filtering data. You can add other more filters:

```php
protected $filters = ['active'];
```

Each filter coresponds to a field. Not all fields can filter data.

#### Image

We've added all fields except image. This type of field needs some explanation. Since fields just process data, they
cannot upload files. This is the responsibility of the repository how to deal with them. Default schema has special
function `files` where you can specify what files repository should upload and where to upload them. This is how we
can say repository to upload our image:

```php
public function files($repo)
{
    $repo->uploads('image');
}
```

From this point, the repository will upload any file or files under `image` key to `public/files` and return path to
that file against public directory (i.e. `files/jAsfn12.png`). Read more about how files are uploaded in
[[this article|repository]].

Don't forget to add a field to the schema:

```php
$schema->image('image');
```

And we can also display image in the list of posts:

```php
$schema->col('image')->format('Image');
```

We've used a formatter here or otherwise you would see a path to file rather than an image. Read more about columns and
formatters [[here|columns]].

But we are not done yet. What if user deletes the item that has some files? We need to delete the file, too. We need to
handle `deleted` event. Ideal place to do this is `boot` method of `Post` model:

```php
public static function boot()
{
    parent::boot();

    // Remove image file when model is deleted
    static::deleted(function ($model)
    {
        if ($model->image) File::delete(public_path($model->image));
    });
}
```

#### Validation

Now we have defined fields and columns, so we can see a list of models, we can create new and update old ones.
But we don't validate data at all. And this is, among other things, is very important. Though Cruddy has advanced
validator, we won't use it's full power for now.

_Cruddy uses Laravel validation system, so defining rules is nothing complex._

We marked `title` and `body` is required, so let's check this to be true:

```php
public function rules($v)
{
    $v->always([
        'title' => 'required',
        'body' => 'required',
        'image' => 'image',
    ]);
}
```

That's it! Nothing else. Crazy simple. [[More about validation|validation]].

### Making it visible

Everything is done for the schema. But we don't have a way to access it besides entering an address in browser.
For this reason Cruddy has configurable menu. There is a section called `menu` in package configuration.
You can reference entity there simply entering it's identifier:

```php
'menu' => [
    'posts',
],
```

You can group several entities:

```php
'menu' => [
    'Content' => [ 'items' => [ 'posts', 'pages' ] ],
],
```

[[More about defining menu items|menu]].

### Dashboard

When you open start page you see nothing but main navigation. You can specify either a view that will be displayed as
dashboard, or you can specify an entity. It is done in configuration file:

```php
// You need to prefix entity identifier with @ to let system know
// that it is an entity rather than a view file
'dashboard' => '@posts',
```

## Further reading

* [[Schema]]
* [[Workflow]]
* [[UI]]
* [[Tutorial: Embedding forms|embedding forms]]