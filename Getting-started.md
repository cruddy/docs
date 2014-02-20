In this tutorial you'll learn how to install Cruddy on top of your Laravel app. If you don't have one, please follow [this guide](http://laravel.com/docs/installation). I also assume that you are familiar with the Laravel and know what is Eloquent model and how it works.

## Installation

Cruddy is available as a composer package. To install it, run following command in the terminal (make shure that current directory is your app's root):

```
composer require kalnoy/cruddy:1.0.*@dev
```

Then you need to publish some assets and config file:

```
php artisan config:publish kalnoy/cruddy
php artisan asset:publish kalnoy/cruddy
```

_You might want to publish assets when composer is updated since Cruddy assets are updated frequently. See [composer scripts](https://getcomposer.org/doc/articles/scripts.md)._

The next step is to enable Cruddy by adding a service provider:

```php
'providers' => array(
    'Kalnoy\Cruddy\CruddyServiceProvider',
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
// app/models/Post.php
<?php

/**
 * Model Post
 */
class Post extends Eloquent {

    protected $table = 'posts';

    protected $fillable = ['title', 'image', 'body', 'active'];
}
```

I have set `$fillable` option to define editable fields. Cruddy will disable a field if its coresponding attribute is not fillable; this field will not pass a value to the repository, but it will be available for validation.

### The schema

Cruddy has a command for generating basic schema. To do this, simply execute following:

```
php artisan cruddy:schema PostSchema --model Post
```

New schema will be created in `app/entities` directory. For now, this is just a class that is not even autoloaded, so we need to do two things: add directory to composer class map and add schema to the entity repository. I hope you know how to make a directory autoloadable.

To add schema to the repository, you need to open cruddy configuration file (`app/config/packages/kalnoy/cruddy/config.php`) and add a line to `entities` section, like so:

```php
'entities' =>
[
    'posts' => 'PostSchema',
],
```

Now you have registered entity with name of `posts` and you can browse it right now going at `http://cruddy-demo.dev/backend/posts`. You'll see a table with two columns and nothing actually editable... That's because we haven't yet defined any fields nor columns.

### Fields

Let's add some fields under `fields` function in schema definition:

```php
public function fields($schema)
{
    $schema->increments('id');
    $schema->string('title')->required();
    $schema->text('body')->required();
    $schema->boolean('active');
    $schema->timestamps();
}
```

Fields definition is much like Laravel's Blueprint that we use to define table columns. Here we have added three fields besides auto generated ones: `title`, `body`, and `active`. They map to actual table columns and are of basic data type. Them you'll learn about more complex data types like relations and inline forms.

Have you noticed `required` modificator? It just teels the UI to display addional note that field is required, it doesn't do any validation!