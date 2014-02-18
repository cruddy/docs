In this tutorial you'll learn how to install Cruddy on top of your Laravel app. If you don't have one, please follow [this guide](http://laravel.com/docs/installation).

## Installation

Cruddy is available as a composer package. To install it, run following command in the terminal (make shure that current directory is your app's root):

```
composer require kalnoy/cruddy:1.0@dev
```

Then you need to publish some assets and config file:

```
php artisan config:publish kalnoy/cruddy
php artisan asset:publish kalnoy/cruddy
```

_You might want to publish assets when composer is updated since Cruddy assets are updated frequently. See [composer scripts](https://getcomposer.org/doc/articles/scripts.md)._