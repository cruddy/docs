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

We will create a simple post model, that will have a title, a body, an image and a boolean flag that determines whether the post is active. This is how table's [blueprint](http://laravel.com/docs/migrations) is looks like:

```php
$table->increments('id');
$table->string('title');
$table->string('image');
$table->text('body');
$table->boolean('active');
$table->timestamps();
```

Pretty common schema, right?