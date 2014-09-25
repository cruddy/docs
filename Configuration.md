## Publishing config

In order to be able to alter Cruddy's configuration, you need to publish the config using artisan command:

```
php artisan config:publish kalnoy/cruddy
```

You can now edit configuration in `app/config/packages/kalnoy/cruddy/config.php` file.

## Configuration options

### System options

`uri` determines the prefix to every Cruddy route. The default value is `backend`.

`dashboard` is a view name that will be displayed at main page. The default value is `cruddy::dashboard`.

You can also provide an entity identifier:

```php
'dashboard' => '@users',
```

`auth_filter` is a filter name that is used before any request to the Cruddy. In this filter you can check whether the 
user is authenticated and has required permissions. The default value is `null`, meaning anyone can access the backend.

`permissions` is an identifier of permissions driver. The driver manages per-entity permissions. [[More about permissions|permissions]].

`entities` is a map of available entities. Each entry is a key-value pair where key is the identifier and value is the 
schema's class name. For example:

```php
'entities' =>
[
    'users' => 'UserSchema',
    'groups' => 'GroupSchema',
],
```

Each schema is resolved from the IoC container.

### Presentational options

`brand` and `brand_url` determine the application name and the url to the main page, the link will be displayed in the left part of the main navigation.

`ace_theme` sets the default color theme for ace editor. The default value is `chrome`.

### Menu configuration

`menu` option configures main menu, `service_menu` can be used to define additional menu items in the right part of the main navigation. [[Read more about configuring menu items|menu]].