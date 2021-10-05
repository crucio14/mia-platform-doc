---
id: create_projection
title: Projection
sidebar_label: Projections
---

## Create a System of Records

To create a projection, you should create a System of Records. This is the data source which updates the projections.

To do so, open the **Projections** section in the fast data group of Mia-Platform Console. Then, select the create button.

The creation of a System of Records requires you to insert a system ID, useful to recognize the system.

## Delete a System of Records

To delete a System of Records, you have to click the Delete button at the bottom-right corner of the System of Records detail page.  
The deletion is not allowed until you have at least one Projection inside the System, hence you need to delete all the projections in a System before being able to delete it.  

## Projections Changes

When a projection is updated, the Real-Time Updater changes a collection called, by default, `fd-pc-SYSTEM_ID` where `SYSTEM_ID` is the name of the System of Records which the Real-Time Updater belongs to. It inserts into it the information about the updated document.

:::caution
The default Projection Changes collection is automatically managed by the Console and it does not support the utilization of custom indexes. If you need to take advantage of them to speed up your queries, you need to use a [custom projection changes collection](./real_time_updater/configuration#custom-projection-changes-collection).
:::

One Projections Changes collection is created for each System of Records as default.

This collection will be used by the Single View Creator to know which single view needs an update. It is the connection between projections and single view.

You can choose to use a collection you have already created in the CRUD section through advanced configuration. To do that, [read here](./advanced#projections-changes).

:::note
When you delete a System of Records, the *Projections Changes* collection linked to it will be deleted as well, because it is no more useful.  
This will not happen if you have chosen to use your own custom Projections Changes collection through the [advanced](./advanced) section.
:::

## Create a Projection

To create a projection using the Console, select the System of Records from which the projection is taken.
In the System of Records detail, search for the `Projections` card and click on the create button.
Here, you can insert the name of your projection.

:::info
The projection name is used as MongoDB collection name.
:::

To view the details of a projection, click on the arrow button at the end of the table row.

### Kafka topics

Once in the projection detail page, there is a card with detail of `Kafka topics`.
Here, you can modify the default name of the topics per environment.
The topics names are pre-compiled with our suggested name:

```txt
projectId.environmentId.projectionName-json
```

where `projectId`, `environmentId` and `projectionName` are filled with, respectively, the id of the console project, the id of the associated environment and the name of the projection.

### Projection metadata

A projection has the [predefined collection properties](../runtime_suite/crud-service/overview_and_usage#predefined-collection-properties) which are required for the `Crud Service`, which is the service responsible for creating the collection on MongoDB.

These fields cannot be deleted and only the `_id` field is editable. You cannot add custom fields to the metadata.  

These fields have no `Cast function` assigned because they are not used for mapping of fields from the Kafka Message received. This means that if the Kafka Message contains a field with the same name as one of the metadata fields, it is not copied on the projection.

### Projection fields

In the card `Fields` in projection, you can add new fields.

Once you click the `Create field` button, a form is prompted where you should insert the following fields (all fields are required):

* `Name`: name of the projection field;
* `Type`: one of `String`, `Number`, `Boolean`, `Date`, `Object`, `Array of object`, `Array of number`, `Array of string`, `ObjectId` or `GeoPoint`
* `Cast function`: it shows the possible [Cast Function](cast_functions) to select for the specified data type;
* `Required`: set the field as required, default to false;
* `Nullable`: declare field as nullable, default to false.
* `Primary Key`: set the field as part of the primary key, default to false.

It's **mandatory** to set at least one Primary Key for each Projection. Otherwise, you will not be able to save your configuration.

:::caution
Setting the Primary Keys does **not** create automatically the unique indexes. You need to create them by yourself.
:::

:::caution
When the `real-time updater` deletes a projection document, it actually makes a **virtual delete** instead of real document deletion. This means that the document is actually kept in the database, but the `__STATE__` field (one of the default fields of the `Crud Service`) is set to `DELETED`.
:::

#### Generate projection fields from data sample

In the card `Fields` in projection, you can upload a data sample to generate fields by clicking on the appropriate button. Doing this will replace the current fields with those contained within the file.
The supported file extension are: `.csv` and `.json`.

Example json

```json
[
  {
    "field1": "anyString",
    "field2": "true",
    "field3": "123"
  }
]
```

Example csv

```csv
field1,field2,field3
false,anyString,123
```

At the end of the upload an internal function will try to cast the types correctly, otherwise it will treat them as strings by default.

:::note
Import of fields is supported only for the following data types: `String`, `Number`, `Boolean` or `Date`. For example, you cannot import fields of type object.
:::

:::caution
You cannot import fields with the same name as one of the metadata fields.
:::

### Indexes

In the card `Indexes`, you can add indexes to the collection. To learn more about crud indexes, [click here](../runtime_suite/crud-service/overview_and_usage#indexes).
However, differently from Indexes that can be created on a normal CRUD, in this section the `Geo` index type is not available.

An `_id` index is created by default and it is not deletable.

Both custom fields and metadata can be used as fields for indexes.

:::caution
In order for the Real Time Updater to correctly update its projections two actions are necessary:

* You should define at least one custom field with flags **Primary Key** and **Required** set to true in the `Fields` card.
* Then, you should create an index using the previously defined custom field and set to true the index **unique** flag.

In this way, the Real Time Updater updates the projection document with the correct primary key value instead of creating a new document.
:::

### Expose projections through API

You can expose a projection through API, only with `GET` method (the data in the projection are modifiable only by the Real Time Updater service).

To expose the Fast Data projection, [create an Endpoint](../development_suite/api-console/api-design/endpoints) with type `Fast Data Projection` linked to the desired projection.

You can expose a projection on a CMS page to help you review the data inside the collection, follow [Configure CMS extensions](../business_suite/cms_configuration/conf_cms#configure-pages).

:::info
The exposed API is not required for Fast Data to work. It is an optional behavior in case you need access to the data without directly accessing it from the database.
:::

### Kafka messages format

Once you have created a System, you need to select the format of the Kafka messages sent from the system.  
To do that, you must correctly configure the Kafka Message Adapter. Go to the `Advanced` section of the `Design` area in Console, open `fast-data` from menu and open the `projections.json` file.

Here, you should write a configuration object as follows:

```json
{
  "systems": {
    "SYSTEM ID": {
      "kafka": {
          "messageAdapter": "THE_FORMAT"
      }
    }
  }
}
```

Where `THE_FORMAT` is the format of your Kafka Messages and can be one of the following: `basic`, `golden-gate`, `custom`.

#### Basic

It's the default one.

The `timestamp` of the Kafka message has to be a stringified integer greater than zero. This integer has to be a valid timestamp.
The `key` of the Kafka message has to be a stringified object containing the primary key of the projection, if `value` also contains the primary key of the projection this field can be an empty string.
The `value` is **null** if it's a *delete* operation, otherwise it contains the data of the projection.
The `offset` is the offset of the kafka message.

Example of a delete operation

```
key: `{"USER_ID": 123, "FISCAL_CODE": "ABCDEF12B02M100O"}`
value: null
timestamp: '1234556789'
offset: '100'
```

Example of an upsert:

```
key: `{"USER_ID": 123, "FISCAL_CODE": "ABCDEF12B02M100O"}`
value: `{"NAME": 456}`
timestamp: '1234556789'
offset: '100'
```

#### Golden Gate

The `timestamp` of the Kafka message is a stringified integer greater than zero. This integer has to be a valid timestamp.  
The `offset` is the offset of the kafka message.
The `key` can have any valid Kafka `key` value.  
The `value` of the Kafka message instead needs to have the following fields:

* `op_type` identifies the type of operation (`I` if insert , `U` if update, `D` if delete).
* `after` it's the data values after the operation execution (`null` or not set if it's a delete)
* `before` it's the data values before the operation execution (`null` or not set if it's an insert)

Example of `value` for an insert operation:

```
{
  'table': 'MY_TABLE',
  'op_type': 'I',
  'op_ts': '2021-02-19 16:03:27.000000',
  'current_ts': '2021-02-19T17:03:32.818003',
  'pos': '00000000650028162190',
  'after': {
    'USER_ID': 123,
    'FISCAL_CODE': 'the-fiscal-code-123',
    'COINS': 300000000,
  },
}
```

#### Custom

If you have Kafka Messages that do not match the format above, you can create your own custom adapter for the messages.
To do that, you need to create a `Custom Kafka Message Adapter`, which is just a javascript function able to convert to Kafka messages as received from the real-time updater to an object with a specific structure.

:::note
You have to create the adapter function *before* setting `custom` in the advanced file and saving.
:::

This adapter is a function that accepts as arguments the kafka message and the list of primary keys of the projection, and returns an object with the following properties:

* **offset**: the offset of the kafka message
* **timestampDate**: an instance of `Date` of the timestamp of the kafka message.
* **keyObject**: an object containing the primary keys of the projection. It is used to know which projection document needs to be updated with the changes set in the value.
* **value**: the data values of the projection, or null

If the `value` is null, the operation is supposed to be a delete.
The `keyObject` **cannot** be null.

In order to write your custom Kafka message adapter, first clone the configuration repository: click on the git provider icon in the right side of the header (near to the documentation icon and user image) to access the repository and then clone it.

Your adapter function file needs to be created below a folder named `fast-data-files`, if your project does not have it, create it.
In this folder, create a folder named as `kafka-adapters/SYSTEM ID` (replacing *SYSTEM ID* with the system id set in Console). Inside this folder create your javascript file named `kafkaMessageAdapter.js`.

For instance if you want to create an adapter for the system `my-system` you need to create the following directory tree:

```txt
/configurations
    |-- fast-data-files
        |-- kafka-adapters/
              |-- my-system/
                    |-- kafkaMessageAdapter.js
```

The file should export a simple function with the following signature:

```js
module.exports = function kafkaMessageAdapter(kafkaMessage, primaryKeys) {
  const {
    value: valueAsBuffer, // type Buffer
    key: keyAsBuffer, // type Buffer
    timestamp: timestampAsString, // type string
    offset: offsetAsString, // type string
  } = kafkaMessage

  // your adapting logic

  return {
    keyObject: keyToReturn, // type object (NOT nullable)
    value: valueToReturn, // type object (null or object)
    timestampDate: new Date(parseInt(timestampAsString)), // type Date
    offset: parseInt(offsetAsString), // type number
  }
}
```

The `kafkaMessage` argument is the kafka message as received from the `real-time-updater`.  
The fields `value` and `key` are of type *Buffer*, `offset` and `timestamp` are of type *string*.

The `primaryKeys` is an array of strings which are the primary keys of the projection whose topic is linked.

Once you have created your own custom adapter for the Kafka messages, commit and push to load your code on git.
Now you need to go on the Console and save in order to generate the configuration for your `Real Time Updater` service that uses the adapter you created.

:::note
After any changes you make on the `adapter` implementation, you need to save from the Console to update the configuration of the services.
:::

Now that you have committed and pushed your custom adapter function you can set `custom` in the advanced file and save.

```json
{
  "systems": {
    "SYSTEM ID": {
      "kafka": {
          "messageAdapter": "custom"
      }
    }
  }
}
```

## Real-Time Updater replicas

### What needs the replicas for

Replicas ensures that a specified number of pod replicas are running at any given time. It is used to guarantee the availability of a specified number of identical Pods.

### How to set replicas

If you do not specify replicas, then it defaults to 1.

To know how to set replicas for the Real-Time Updater, [read here](./advanced#real-time-updater-replicas).

## Real-Time Updater CPU and Memory Requests and Limits

Real-Time Updater has default settings for the CPU and memory requests and limits. These defaults are set at Console installation time.
For Console PaaS, these are already set with values:

* Memory Limit: 250Mi
* Memory Request: 80Mi
* CPU Limit: 200m
* CPU Request: 40m

To know the limits on your on-premise Console installation, please contact your Mia Platform referent.

## Technical limitation

In your custom files (e.g. `kafka-adapters`) you can import only the node modules present in the following list:

* [lodash.get](https://github.com/lodash/lodash/tree/4.4.2-npm-packages/lodash.get)
* [mongodb](https://github.com/mongodb/mongo/tree/r3.6.0)
* [ramda](https://github.com/ramda/ramda/tree/v0.27.1)

:::caution
It is used the node version 12.
:::