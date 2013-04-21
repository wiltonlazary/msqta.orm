MSQTA.ORM
=========

IndexedDB-WebSQL abstract layer ORM

License
----------
MSQTA.ORM is free and open source software distributed under the [BSD License](http://opensource.org/licenses/BSD-3-Clause).

Build MSQTA.ORM
-----------------------
In order to build MSQTA.ORM run the following command:
(you need the packages [dryice](https://github.com/mozilla/dryice) and [shelljs](https://github.com/arturadib/shelljs))

	$ node build.js
	
The final `.js` will be located in the `./release` directory.

How to use it
============
Just place the following script tag pointing to wherever you have `msqta.orm.js` build file

	<script src="msqta.orm-1.0.0.js"></script>
	
Getting started
--------------------
Remember this: **msqta.orm stores js objects on schemas**.
A js object is just a that, for example:
```javascript
	{ name: 'John Doe', age: 12, food: [ 'pizza', 'hamburger' ] }
```	
A schema is like a table that stores these objects, and every property name that composes the object refers to a column name of that schema, each column support a particular variable type, these are:
* string
* integer
* float
* array
* object
* boolean

Every schema (the tables) are placed into a database, creating the database is the first step that you need to do.

Creating a database
--------------------------
Creating is also opening a database, you only create a database the first time that you invock it. So to create/open a database do this:
```javascript
	var db = new MSQTA.ORM( settings, [callback], [context] );
```
`settings` is an object with the following information:
* `name`: the name of the database.
* `devMode` *(optional)*: output to the browser console what MSQTA.ORM are doing.
* `prefered` *(optional)*: some browsers supports WebSQL and IndexedDB, use this field to force it to use a specific implementation, if the browser don't support the prefered implementation it will use the implementation that it really support.
* `forceDestroy` *(optional)*: only useful in dev stage, it drop the named database to be recreated again, brings to you always an empty database. This only possible with an IndexedDB implementation.

`[callback]` an function to be called with a specified `[context]` after the database is created/opened.

## Creating a schema
A schema is just like a table, to set a schema do this:
```javascript
	var schema = new db.Schema( settings, [options] )
```	
`settings` is an object that dictates the name of the schema, its fields *(columns)* and the primary key, for example something like this (all are required):
```javascript
	name: 'clients',
	fields: {
		id: { type: 'integer' },
		name: { type: 'string', index: true },
		email: { type: 'string' },
		tel: { type: 'string', allowNull: true, index: true },
		address: { type: 'string' },
		comments: { type: 'string' }
	},
	primaryKey: 'id'
```	
* `name`: name of the schema
* `fields`: the columns definition of the schema according to this:

```javascript
	name_of_the_field(column): { 
		type: 'string|integer|float|object|array|boolean', // the field type
		index: true|false, // use this field as an index? (optional)
		unique: true|false, // only unique values are permitted? (optional)
		allowNull: true|false // can i store the null value? (optional)
	}
```

* `primaryKey`: the name of the field *(column)* that is the primary key. **You always has to specified a primary key and it must be of the type integer!**

`options` *(optional)*: an object that with the following information:
* `forceDestroy`: destroy the current schema to be recreated again, its useful is you want always an empty table.
* `forceEmpty`: this is a like forceDestroy, but will the difference that this one will not drop the schema, instead it will just empty that schema
* `callback`: a function to be called after the schema is setted with...
* `context`: ...a specified context

**THIS IS VERY IMPORTANT**: When you change a schema field type, MSQTA.ORM will recast the current values of that field to the new specified type, so in theory you will never lost information, for example if is the previously field type was `string` and now is `integer`, all the current values will casted to `0` or maybe `null` is the property `allowNull` is setted up.


	