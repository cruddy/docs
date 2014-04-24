Validation rules are defined in [[the schema|schema]]. Cruddy uses Laravel validator by default.

## Defining rules

### Basic rules

You can define validation rules under schema's `rules` method:

```php
public function rules($v)
{
    $v->rules(
    [
        'name' => 'required|max:255',
        'slug' => 'required|max:255|slug',
        'description' => 'required',
        'image' => 'required|image',
    ]);
}
```

See the list of [available rules](http://laravel.com/docs/validation#available-validation-rules).

### Separating rules by action

Sometimes there is need to add some rules only for new models, or only for existing models. Default validator allows to define separate rules for `create` and `update`:

```php
public function rules($v)
{
    // Default rules
    $v->rules(
    [
        'password' => 'max:255',
    ]);

    // This rules will be applied only when model is created
    $v->create(
    [
        'password' => 'required',
    ]);

    // This rules will be applied only when model is updated
    $v->update(
    [
        'password' => 'required_with:change_password',
    ]);
}
```

Specific rules are _merged_ with default rules based on whether the model exists or not.

### Referencing input

It's such a pain when you need to define `unique` rule... When model is updated, you need to specify an id of the model to exclude it from validating. You can now reference input data by inserting string key in curly braces:

```php
$v->create(
[
    'slug' => 'unique:products,slug'
]);

$v->update(
[
    'slug' => 'unique:products,slug,{id}'
]);
```

## Custom validator

Custom validator must implement `Kalnoy\Cruddy\Service\Validation\ValidableInterface`. Validator is returned from schema's `validator` method. See [SchemaInterface](https://github.com/lazychaser/cruddy/blob/1.0/src/Kalnoy/Cruddy/Schema/SchemaInterface.php).