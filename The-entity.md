Cruddy entity consists of many components. Each component has an interface and default implementation.
It is possible to create your own implementation, but usually you'll be satisfied with the default implementation or
extending it.

* [Schema](#schema)
* [Field](#field)
* [Column](#column)
* [Validator](#validator)
* [Repository](#repository)

## Schema

[[Read main article|schema]]

Entity's schema is a component that is responsible for creating other components. This is the place where you can
override things. It is also provides configuration for the UI, so you can control how the entity's page looks.

## Field

[[Read main article|fields]]

Basically, entity's fields is a layer between the UI and the repository. The field extracts data from the model and
presents it in suitable form for the UI. The field is also processes the input and decides whether it should be sent
to the repository or not.

Cruddy has many different fields that determine different data types; each field has corresponding JavaScript class.
We can devide different field types into two big categories: basic types and relational types. Basic types are just
mapping to database table columns, possibly with some extended functionality (like markdown editor). Relational types
are for managing related models.

## Column

[[Read main article|columns]]

Column is much like a field, but it works only in one-way direction. Columns extract data for a list of model rather
than for single one. They are also responsible for sorting data.

There is two types of column: _proxy_ and _computed_. Proxy column uses a field to extract data, while computed column
uses closures.

## Validator

[[Read main article|validation]]

Before data is sent to the repository, it must be validated first. Validator validates data for specific action whether
it `create` or `update`. Default implementation is enough for most cases since it allows to specify default rules and
specific rules for each action, and also to reference input values using placeholders
(you can reference `{id}` value for rules like `unique`).

## Repository

[[Read main article|repository]]

Repository is responsible for performing actual CRUD operations. It retrieves data, creates, updated and deletes models.
Default implementation just uses Laravel models to do this, but it also has methods to sync simple relationships
(`BelongsTo`, `BelongsToMany`, etc).

