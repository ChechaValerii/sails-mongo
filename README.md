![https://adoric.com](https://i.imgur.com/55dd7EG.png)

# @adoric/sails-mongo

Sails.js/Waterline adapter for MongoDB. (See [compatibility](#compatibility) for more details.)

> Support transactions API
>
> Provides easy access to MongoDB from Sails.js & Waterline.
> This module is a Sails/Waterline adapter maintained by the core team.  Its goal is to provide robust, easy-to-use access to MongoDB from Sails.js and Waterline.
>
> As an adapter, this module implements a set of declarative interfaces, conventions, and best-practices for integrating with Mongo databases.
> Strict adherence to an adapter specification enables the (re)use of built-in generic test suites, standardized documentation, reasonable expectations around the API for your users, and overall, a more pleasant development experience for everyone.


## Installation

To install this adapter, run:

```bash
$ npm install @adoric/sails-mongo
```

Then [connect the adapter](http://sailsjs.com/documentation/reference/configuration/sails-config-datastores) to one or more of your app's datastores.

## Transactions

We have upgrade [mongodb](https://www.npmjs.com/package/mongodb) to 3.5.9
Will think about Waterline
Example works with Native MongoDb

```
  const { manager } = sails.getDatastore(datastoreName);
  const { client } = manager;

  // Step 1: Start a Client Session
  const session = client.startSession();

  // Step 2: Optional. Define options to use for the transaction
  const transactionOptions = {
    readPreference: 'primary',
    readConcern: { level: 'local' },
    writeConcern: { w: 'majority' }
  };

  // Step 3: Use withTransaction to start a transaction, execute the callback, and commit (or abort on error)
  // Note: The callback for withTransaction MUST be async and/or return a Promise.
  try {
    await session.withTransaction(async () => {
      const coll1 = client.db('mydb1').collection('foo');
      const coll2 = client.db('mydb2').collection('bar');

      // Important:: You must pass the session to the operations

      await coll1.insertOne({ abc: 1 }, { session });
      await coll2.insertOne({ xyz: 999 }, { session });
    }, transactionOptions);
  } finally {
    await session.endSession();
    await client.close();
  }
```

## Usage

Visit [Models & ORM](http://sailsjs.com/docs/concepts/models-and-orm) in the docs for more information about using models, datastores, and adapters in your app/microservice.


## Compatibility

> This version of the adapter has been tested with MongoDB versions 3.6, 4.0, and 4.2.

This adapter implements the following methods:

| Method               | Status            | Layer         |
|:---------------------|:------------------|:--------------|
| ~~registerDatastore~~| ~~Implemented~~   | _N/A_         |
| ~~teardown~~         | ~~Implemented~~   | _N/A_         |
| validateModelDef     | Implemented       | Modeled       |
| createRecord         | Implemented       | Modeled (DML) |
| createEachRecord     | Implemented       | Modeled (DML) |
| updateRecords        | Implemented       | Modeled (DML) |
| destroyRecords       | Implemented       | Modeled (DML) |
| findRecords          | Implemented       | Modeled (DQL) |
| join                 | _not supported_   | Modeled (DQL) |
| countRecords         | Implemented       | Modeled (DQL) |
| sumRecords           | Implemented       | Modeled (DQL) |
| avgRecords           | Implemented       | Modeled (DQL) |
| definePhysicalModel  | Implemented       | Migratable    |
| dropPhysicalModel    | Implemented       | Migratable    |
| setPhysicalSequence  | _not supported_   | Migratable    |


## Questions?

See [Extending Sails > Adapters > Custom Adapters](http://sailsjs.com/documentation/concepts/extending-sails/adapters/custom-adapters) in the [Sails documentation](http://sailsjs.com/documentation), or check out [recommended support options](http://sailsjs.com/support).

#### Development and Test

This repository includes a Docker Compose file that helps setting up the environment needed to run the test.

The `npm test` command expects a local MongoDB instance running.

For convenience, some new npm scripts are available:
- `npm run start-mongodb`: Starts MongoDB docker instance
- `npm run stop-mongodb`: Stops MongoDB docker instance
- `npm run mongodb-shell`: Runs the MongoDB shell CLI, connects to the instance started by the `npm run start-mongodb` command.

This simplifies development as you do not need to have a MongoDB instance running on the development computer.

Notice that if you do have a local MongoDB instance, then, there might be port conflicts if you run the docker version.
The docker version is configured to run on the standard port 27017.

The normal development workflow would now be:
- When starting a development session, `npm run start-mongdb`
- Now we can execute `npm test` as many times as needed
- When finishing a development session, `npm run stop-mongdb`

The `npm run docker-test` command runs the tests on a single run under the latest MongoDB version (at the time 4.2).
It automatically starts a MongoDB docker instance, and it stops it. This is useful for one time local tests.
Note that since this command stops MongoDB, `npm test` will fail.

When running automation tests in Travis, the module is tested under a combination of Node.js 10, 12, 14 and
MongoDB: 3.6, 4.0, 4.2.

When running automation tests in AppVeyor, the module is tested under a combination of Node.js 10, 12, 14 and
the MongoDB version that AppVeyor supports. Multiple MongoDB version are not tested in AppVeyor.

For more information, check [MongoDB's Support Policy](https://www.mongodb.com/support-policy).

To run tests while developing, you can run `npm run docker`. This command opens a docker instance and opens a shell.
From there you can run `npm test` to run the tests as many times as you need.

## License

This [core adapter](http://sailsjs.com/documentation/concepts/extending-sails/adapters/available-adapters) is available under the **MIT license**.

As for [Waterline](http://waterlinejs.org) and the [Sails framework](http://sailsjs.com)?  They're free and open-source under the [MIT License](http://sailsjs.com/license).

&copy; [adoric.com](https://adoric.com/)

![image_squidhome@2x.png](http://i.imgur.com/RIvu9.png)
