[Read this](http://laravel.com/docs/localization#overriding-package-language-files)

# Entity
Create ```entities.php``` file here:
```
app/lang/packages/en/cruddy/entities.php
```
with this content:
```
<?php return array(

    'help' => array(
        'permissions_string' => 'Permission list. One on line',
    ),

    'columns' => array(
        'full_name' => 'Full name',
        'updated_at' => 'Updated',
        'created_at' => 'Created',
    ),
);
```

Other files you can find in sample app [here](https://github.com/lazychaser/cruddy-app/tree/master/app/lang/en)