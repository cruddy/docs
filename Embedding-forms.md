In this tutorial we will see in detail what are inline fields and how they are usefull.

![Embedded meta data entity](https://drive.google.com/uc?id=0B8WgmUNiDzmyc1JwRU5DaFVqQjg)

Laravel Eloquent supports many relation types. Let's talk about one-to-one relation type. How is it usefull? Well, I was
asked this question long time ago when I was interviewed, and I didn't know the answer. But it's rather simple. I like
to think about one-one relationship as an extension to the base table. This means that base table (i.e. post) may have
additional data that is stored in separate table (i.e. html meta data), and may not have one. You can add as many extensions
as you want without altering and adding new columns to the base table, therefore keeping things simple.

Cruddy allows to edit your extensions together with your base model. Imagine this as embeding other entity's fields into one form.

## Prerequisites

Let's extend our `Post` model from [[getting started]] tutorial. We'll add meta data extension to it. The model will
hold `title`, `description` and `keywords` attributes that can be used in frontend. We'll be more provident and make
this model morphable, since many types of content can have metadata. So our `Post` establishes morph-to-one relationship
with `MetaData` model.

Our goal is to embed our metadata into post data for comfort. Let's begin with creating model.

## Eloquent model

At first, we need to create migration:

```
php artisan make:migration create_meta_table --create meta
```

The schema looks like this:

```php
Schema::create('meta', function(Blueprint $table)
{
    $table->increments('id');
    $table->morphs('meta_holder');
    $table->string('title');
    $table->string('keywords');
    $table->string('description');
    $table->timestamps();
});
```

We can now run migrations:

```
php artisan migrate
```

Here is how model looks, nothing extraordinary:

```php
// app/Meta.php
<?php

class Meta extends Eloquent {

    protected $table = 'meta';

    protected $fillable = ['title', 'keywords', 'description'];
}
```

Now we need to add relation to the `Post` model:

```php
public function meta()
{
    return $this->morphOne('Meta', 'meta_holder');
}
```

## Entity

Now when model is set up, we need to generate a schema for it. Since the meta entity will be embeded, we only need to
define fields and rules (not required).

Run this to generate new schema:

```
php artisan make:entity Meta
```

After cleanup and defining fields we have following:

```php
// app/Entities/Meta
<?php

namespace App\Entities;

use Kalnoy\Cruddy\Schema\BaseSchema;
use Kalnoy\Cruddy\Service\Validation\FluentValidator;

class MetaSchema extends BaseSchema {

    protected $model = 'App\Meta';

    /**
     * Define some fields.
     *
     * @param $schema
     */
    public function fields($schema)
    {
        $schema->increments('id');
        
        $schema->string('title')->label('Meta title');
        $schema->string('keywords')->label('Meta keywords');
        $schema->text('description')->rows(3)->label('Meta description');

        // We don't want timestamps to be visible
        $schema->timestamps(false);
    }

    /**
     * Define validation rules.
     *
     * @param $v
     */
    public function rules($v)
    {
        $v->rules(
        [
            'title' => 'max:255',
            'keywords' => 'max:255',
            'description' => 'max:255',
        ]);
    }
}
```

The schema is not accessible for now, we need to add it to the repository. Open package configuration and add the line
to the `entities` section:

```php
'entities' => [
    'posts' => 'PostSchema',
    'meta' => 'MetaSchema',
],
```

The only thing is left is to embed meta entity into post form. This is done in `PostSchema` in fields configuration:

```php
$schema->embed('meta', 'meta');
```

First parameter is a relation name, the second one is the related entity's id.

That's it! You can now add meta data to your posts.

## Conclusion

As you can see, embeding forms in nothing complex. We have inlined single form, but Cruddy supports one-to-many and
morph-to-many relationships. I think this feature is going to increase productivity of content managers.