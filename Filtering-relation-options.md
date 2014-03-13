Cruddy allows to connect models between using drop down menus, where you can pick one or more models depending on relation type. Sometimes there is a need to limit the set of available options. Cruddy is able to apply static and dynamic filters.

## Static options filter

Static filter is a closure that is aplied to the query builder:

```php
$schema->relates('options', 'options')->filterOptions(function ($q)
{
    $q->where('type', '=', 'my_special_type');
});
```

So you can alter a builder or add some constraints.

## Dynamic constraints

Sometimes there is a need to filter options based on value of other field. Consider this example: _state_ belongs to _country_, _person_ belongs to _state_. You would like to see a list of states of specific country rather than all states of all countries. So, you select a country first, and then you get a list of states of that country.

Main idea is that you define a field on target entity that coresponds to the field on related entity, and this field will act as a filter for options.

On `states` entity:

```php
$schema->relates('country', 'countries');
```

On `persons` entity:

```php
$schema->relates('country', 'countries');
$schema->relates('state', 'states')->constraintWith('country');
```

You can chain constraints:

```php
$schema->relates('city', 'cities')->constraintWith('state');
```

Remember that when applying contraints, two fields are acting: on target entity and on related entity. By default it is considered that their identifiers are equal. If they're not, you need to provide identifier of field on related entity:

```php
$schema->relates('state', 'states')->constraintWith('country', 'parent');
```

On `states` entity:

```php
$schema->relates('parent', 'countries');
```