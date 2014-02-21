From this article you will learn how everything works in Cruddy depending on user actions, and how each component communicates with other.

## Communications

The Cruddy [[UI]] communicates with server using AJAX requests. So it is completely decoupled from the server implementation as long as it follows API. It is possible to use the [[UI]] with other platforms.

## Getting a list of items

```
GET http://cruddy-demo.dev/backend/api/posts
```

When user opens an entity page or moving to next page, datagrid makes an AJAX request to the api controller. It passes desired page, search string and filters data. This request is passed to the [[repository]] that performs search and returns a collection of eloquent models. But it doesn't apply any filters besides pagination. This is the responsibility of a `SearchProcessorInterface` that takes query builder and options, and applies all needed modifiers on that builder. For this time search processor is a column collection. It applies order and filters based on columns that the collection holds.

After the collection of models is received, it's time to extract it's data. This is also the responsibility of a column collection. As a result we get an array of items, each of which is also an array. This data is then encoded to JSON and sent as a result.

## Getting single item

```
GET http://cruddy-demo.dev/backend/api/posts/1
```

When user requests an item for editing, the entity finds an eloquent model using a [[repository]] first. Then, it extracts data of that item using _[[fields]]_ collection and returns it as array. Inline relation will extract model's data using referenced entity, so in result you will have an item that holds other item/items. Basic relation fields will extract only an _id_ and a _title_.

## Saving an item

```
PUT http://cruddy-demo.dev/backend/api/posts
POST http://cruddy-demo.dev/backend/api/posts/1
```