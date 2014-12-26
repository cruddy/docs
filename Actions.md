Action is an extra method of saving model that allows to set some extra properties on the model
before it is saved.

![](https://drive.google.com/uc?id=0B8WgmUNiDzmyRTVEM3NIaFZMYkU)

### Defining actions

The shema now has additional method `actions`, where you can specify additional actions,
that can be performed when saving the model.

```php
protected function actions($actions)
{
    ...
}
```

In general, action is a class that implements `Kalnoy\Cruddy\Actions\Action` interface.
There is a convenient stub based on `Fluent`. Here's the example of basic action:

```php
$actions->define('foo', function ($model)
{
    $model->bar = 'baz';
});
```

It's basically an `id` and a `callback`, that is executed before model is saved. The callback can be a class name, that 
is resolved from the IoC:

```php
$actions->define('foo', 'FooAction');
```

```php
class FooAction
{
    public function execute($model)
    {
        $model->foo = 'bar';
    }
}
```

##### Title

```php
$actions->define(...)->title('Static Title');
$actions->define(...)->title(function ($model) { return $model->title; });
```

##### Hiding action

You can hide the action for some reason, for example when user doesn't have necessary permissions:

```php
$action->hide(function ($model) { return not Auth::user()->isActionPermitted('bar'); });
```

##### Disabled states

If you want action to be visible but not available for execution, you can specify that it's disabled:

```php
$action->disable(function ($model) { return $model->is_active; });
```

##### States

There is a possibility to alter the state of action which is basically the style of the button in the form.
Default states are: `default`, `danger`, `success`, `warning`, `info` and `primary`. You can also specify your own 
state `foo`, the button will receive a class `btn-foo`.

```php
$action->state('warning');
$action->state(function ($model) { return $model->getState(); });
```

#### Reusable actions

It is possible to reuse actions across entities by extracting it to separate class, that implements 
`Kalnoy\Cruddy\Actions\Action` interface:

```php
$actions->add('FooAction');
$actions->add(new BarAction('some parameter'));
```

### An example from test app:

```php
protected function actions($actions)
{
    $actions->define('reset_price', function ($model)
    {
        $model->price = 0;
    })
    ->title(function ($model)
    {
        return "Reset price from ".$model->price." to zero";
    })
    ->disable(function ($model)
    {
        return $model->price == 0;
    });

    $actions->define('random_price', function ($model)
    {
        $model->price = rand(1, 150);
    })
        ->title('Randomize price');

    $actions->add('TestAction');
}
```