# react-odoo

React library for Odoo using the axios API and JSON-RPC.

## Installation

```bash
$ npm install react-odoo
```
or
```bash
$ yarn add react-odoo
```

## Usage

Please refer to the Odoo [API documentation](https://www.odoo.com/documentation/11.0/webservices/odoo.html) if you need help structuring your database queries. I'll try to make a more thorough explanation in the future about how it works.

**Creating Odoo connection instance**
Before executing any kind of query operations, a connection instance must be established either with a username/password or with a previously retrieved session id.
```js
import Odoo from 'odoo-react'
const odoo = new Odoo({
  host: 'YOUR_SERVER_ADDRESS',
  port: 8069, /* Defaults to 80 if not specified */
  database: 'YOUR_DATABASE_NAME',
  username: 'YOUR_USERNAME', /* Optional if using a stored session_id */
  password: 'YOUR_PASSWORD', /* Optional if using a stored session_id */
  session_id: 'YOUR_SESSION_ID', /* Optional if using username/password */
  context = 'Your_Context', /* Optional Like Change Language */
})

```

**authenticate**
Returns a promise which resolves into an object containing the current users' data, including a session id which can be stored for future connections and session persistence.
```js
odoo.authenticate()
.then(response => { /* ... */ })
.catch(e => { /* ... */ })
```

**read**
Receives an Odoo database `model` string and a `params` object containing the `ids` you want to read and the `fields` you want to retrieve from each result.
Returns a promise which resolves to an array of results matching the array of ids provided in the parameters.
```js
/* read partner from server */
const  params = {
  ids: [1,2,3,4,5],
  fields: [ 'name' ],
}
odoo.read('res.partner', params, context)
.then(response => { /* ... */ })
.catch(e => { /* ... */ })
```

**search_read**
Just like the Get method, this one also receives a `model` string and a `params` object. With this method the parameters may include a `domain` array for filtering purposes (with filter statements similar to SQL's `WHERE`),  `limit` and `offset` values for pagination and an `order` property which can be set to specific fields. The array of `ids` becomes optional.
Returns a promised which resolves to an array of results matching the parameters provided.
```js
const  params = {
  ids: [1,2,3,4,5],
  domain: [ [ 'list_price', '>', '50' ], [ 'list_price', '<', '65' ] ],
  fields: [ 'name', 'list_price', 'items' ],
  order:  'list_price DESC',
  limit:  5,
  offset:  0,
}

odoo.search_read('product.product', params, context)
.then(response => { /* ... */ })
.catch(e => { /* ... */ })
```


**Create**
Receives a `model` string and a `params` object with properties corresponding to the fields you want to write in the row.

```js
odoo.create('delivery.order.line', {
  sale_order_id: 123
  delivered:  'false',
}, context)
.then(response => { /* ... */ })
.catch(e => { /* ... */ })
```

**Update**
Receives a `model` string, an array of `ids` related to the rows you want to update in the database and a `params` object with properties corresponding to the fields that are going to be updated.

If you need to update several rows in the database you can take advantage of the Promises API to generate and populate an array of promises by iterating through each operation and then resolving all of them using `Promise.all()`
```js
odoo.update('delivery.order.line', [ids], {
  delivered:  'true',
  delivery_note:  'Delivered on time!'
}, context)
.then(response => { /* ... */ })
.catch(e => { /* ... */ })
```

**Delete**
Receives an Odoo database `model` string and an `ids` array corresponding to the rows you want to delete in the database.

```js
odoo.delete('delivery.order.line', [ids], context)
.then(response => { /* ... */ })
.catch(e => { /* ... */ })
```

**RPC Call Generic**
If you wish to execute a custom RPC call not represented in this library's methods, you can also run a custom call by passing an `endpoint` string and a `params` object. This requires understanding how the Odoo Web API works more thoroughly so you can properly structure the functions parameters.

```js
odoo.rpc_call('/web/dataset/call_kw', params)
.then(response => { /* ... */ })
.catch(e => { /* ... */ })
```

## References

*  [Odoo ORM API Reference](https://www.odoo.com/documentation/11.0/reference/orm.html)

*  [Odoo Web Service External API](https://www.odoo.com/documentation/11.0/webservices/odoo.html)

## License
This project is licensed under the MIT License 

## Acknowledgements
This project was built upon previous versions. It is a fork of AlexisReverte's Node.js library.