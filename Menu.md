Cruddy allows to define two menus in the configuration: main menu and service menu. Service menu is displayed to the right of the main menu; you can place there logout link and link to the settings page, for example. Same definition rules are applied to both menus.

## Defining menu items

### Referencing entity

To insert a link to the entity, you can simply insert it's id, like so:

```php
'menu' =>
[
    'users',
]
```

You'll get a link to the entity with generated label. If you want more control,
you can expand this to an array:

```php
'menu' =>
[
    [ 'entity' => 'users', 'label' => 'User list' ],
]
```

__Important!__ The link is not shown if user is not permitted to view the entity.

### Custom links

You can add a link to any other resource by defining either `href`, `url` or `route` key in the menu item array:

```php
'menu' =>
[
    [ 'label' => 'Dashboard', 'url' => 'dashboard' ],
    [ 'label' => 'Custom page', 'route' => [ 'route.id', 'param' => 'value' ] ],
]
```

There is a shortcut for defining URL's:

```php
'menu' =>
[
    'Dashboard' => 'dashboard',
]
```

#### Disabling menu item

Sometimes you need to hide a menu item if user doesn't have enough permissions.
You can set `permissions` options which is a closure that should return boolean
value to indicate whether the item is visible or not.

```php
'menu' =>
[
    [
        'label' => 'Import',
        'route' => 'import',
        'permissions' => function () { return Auth::user()->isAdmin(); },
    ],
]
```

### Dropdown menus

When getting more and more links, it's time to group some of them together:

```php
'menu' =>
[
    'Auth' => [ 'users', 'groups' ],
]
```

Or more expanded version:

```php
'menu' =>
[
    [
        'label' => 'Auth',
        'items' => [ 'users', 'groups' ],
        // Any other attributes
    ],
],

### Separators

You can insert vertical or horizontal separator like so:

```php
'menu' =>
[
   'products', 'categiries',
   '-', // this is a separator
   'users', 'groups',
]
```

### Specifying icons

You can attach [bootstrap's glyphicon](http://getbootstrap.com/components/#glyphicons) to a menu item:

```php
'menu' =>
[
    [ 'label' => 'Dashboard', 'url' => 'dashboard', 'icon' => 'dashboard' ],
]
```

### Additional attributes

Any other key in the array will be set as an attribute on the menu item element,
so it is possible to provide additional information:

```php
'menu' =>
[
    [
        'label' => 'Dashboard',
        'url' => 'dashboard',
        'icon' => 'dashboard',
        
        'id' => 'dashboard',
        'data-foo' => 'bar',
    ],
]
```

## Service menu

Besides main menu you can define a service menu that is displayed in the right corner.
The rules for building menu items are the same. Service menu is defined under
`service_menu` configuration option.