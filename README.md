# Cayley

This is a Node.js client for open-source graph database [cayley](https://github.com/cayleygraph/cayley).

# Documentation

## Feature list

* ES6 based.
* TLS supported.
* Multiple cayley hosts supported.
* Default random client selection strategy.
* Callback style and bluebird Promise style APIs.
* Transparent JSON to nquads data formatting handling.
* 
* Fully covered '[mocha](https://github.com/mochajs/mocha) + [chai](https://github.com/chaijs/chai)' test cases.
* Clearly designed entry-level nquads data: [friend_circle_with_label.nq](./test/data/friend_circle_with_label.nq), [friend_circle_without_label.nq](./test/data/friend_circle_without_label.nq) for getting you in.

## Basic usages examples

```
npm install node-cayley --save
```

```
// Watch: here the lib will return a cayley clients array for supporting multiple cayley hosts,
// for more information like the default random client slection strategy, please continue reading this doc.

const cayleyClients = require('node-cayley')('http://localhost:64210');
```
  * Write your JSON object directly to cayley, this lib will handle the JSON to nquad transparently for you.

    ```
    # Callback style.
    cayleyClients[0].write([
      {
        primaryKey: '</user/shortid/23TplPdS>',
        label: 'companyA',

        userId: '23TplPdS',
        realName: 'XXX_L3',
        mobilePhone: {
          isVerified: false,
          alpha3CountryCode: '+65',
          mobilePhoneNoWithCountryCallingCode: '+6586720011'
        }
      }
    ], (err, resBody) => {
      if (err) {
        // Something went wrong...
      } else {
        // You get the response body here.
      }
    });
    ```

    ```
    # Bluebird Promise style.
    cayleyClients[0].write([
      {
        primaryKey: '</user/shortid/23TplPdS>',
        label: 'companyA',

        userId: '23TplPdS',
        realName: 'XXX_L3',
        mobilePhone: {
          isVerified: false,
          alpha3CountryCode: '+65',
          mobilePhoneNoWithCountryCallingCode: '+6586720011'
        }
      }
    ]).then((resBody) => {
      // You get the response body here.
    }).catch((err) => {
      // Something went wrong...
    });
    ```

  * Use Gremlin APIs, refer to official [Gremlin APIs doc](https://github.com/cayleygraph/cayley/blob/master/docs/GremlinAPI.md) for more information.

    ```
    # Callback style.
    cayleyClients[0].g.type('query').V().All((err, resBody) => {
      // Callback body here...
    });
    ``` 

    ```
    # Bluebird Promise style.
    cayleyClients[0].g.type('query').V().All().then((resBody) => {
      // You get the response body here.
    }).catch((err) => {
      // Something went wrong...
    });
    ```

  * For all the APIs usages example you can find in the [test folder](./test/apis).

## Configuration

  * Options

    * apiVersion: `'v1'`, Set API version.

    * logLevel: `integer`, Set log level, the lib use logger [winston](https://github.com/winstonjs/winston) and npm log levels(winston.config.npm.levels).

    * promisify: `boolean`, Set 'true' to use [bluebird](https://github.com/petkaantonov/bluebird) style APIs, 'false' to use callback style APIs.

    * secure: `boolean`, Set whether your cayley server support TLS, 'true' will coz the lib to use 'https' for all transport.

    * certFile: `string`, Path of your TLS cert file, if above 'secure' was set to true, this must be provided.

    * keyFile: `string`, Path of your TLS key file, if above 'secure' was set to true, this must be provided.

    * caFile: `string`, Path of your TLS root CA file, if above 'secure' was set to true, this must be provided.

    * servers: `Array contains multiple cayley hosts object`, for supporting multiple cayley hosts, you can override the top level 'secure', 'certFile', 'keyFile' and 'caFile' options here.

  * All of the following configuration are valid configuration format.

    ```
    # Case0.
    require('node-cayley')('http://localhost:64210');

    # Case1.
    require('node-cayley')('http://localhost:64210', {
      logLevel: 5,
      promisify: true
    });

    # Case2.
    require('node-cayley')('host_0:port_0', {
      logLevel: 5,
      promisify: true,
      secure: true,
      certFile: '',
      keyFile: '',
      caFile: '',
      // The uri('host_0:port_0') will be merged into the below 'servers' with the top level options.
      servers: [
        {
          host: host_1,
          port: port_1,
          /*
           * Options put here will be applied to this specific server and will override the top level options,
           * like 'secure: false' will coz the lib use 'http' for this specific server.
           */
          secure: false
        }
      ]
    });

    # Case3.
    require('node-cayley')({
      logLevel: 5,
      promisify: false,
      secure: true,
      certFile: '',
      keyFile: '',
      caFile: '',
      servers: [
        {
          host: host_0,
          port: port_0,
          certFile: '',
          keyFile: '',
          caFile: ''
        }
      ]
    });
    ```

## Default random client selection strategy

  * As you can see above this lib support multiple cayley hosts configuration, the lib also provide a default random client selection strategy, which you can use as this:

    ```
    const cayleyClients = require('node-cayley')({
      servers: [
        {
          host: host_0,
          port: port_0
        },
        {
          host: host_1,
          port: port_1
        }
      ]
    });

    const pickRandomly = cayleyClients.pickRandomly;

    // Then when you need one cayley client, just do:
    const client = pickRandomly(cayleyClients);
    ```

## HTTP APIs

Depends on your `promisify` setting provide `callback style` or `bluebird Promise style` API.

#### write([{}, {}, ...], callback)

#### writeFile('pathOfNquadFile', callback)

#### delete([{}, {}, ...], callback)

## Gremlin APIs

Depends on your `promisify` setting provide `callback style` or `bluebird Promise style` API.

### Graph object

#### graph.Vertex([nodeId],[nodeId]...)

#### graph.Morphism()

### Path object

#### path.Out([predicatePath], [tags])

#### path.In([predicatePath], [tags])

#### path.Both([predicatePath], [tags])

#### path.Is(node, [node..])

#### path.Has(predicate, object)

#### path.LabelContext([labelPath], [tags])

#### path.Limit(limit)

#### path.Skip(offset)

#### path.InPredicates()

#### path.OutPredicates()

#### path.Tag(tag)

#### path.Back(tag)

#### path.Save(predicate, tag)

#### path.Intersect(query)

#### path.Union(query)

#### path.Except(query)

#### path.Follow(morphism)

#### path.FollowR(morphism)

### Query object

#### query.All(callback)

#### query.GetLimit(size, callback)

#### query.ToArray(callback)

#### query.ToValue(callback)

#### query.TagArray(callback)

#### query.TagValue(callback)

#### query.ForEach(gremlinCallback, callback)

#### query.ForEach(limit, gremlinCallback, callback)

## Additional resources

Here are some resources which I read when I was trying to understand and learn some critical concepts related to graph database, hope can help you also, like:

  * What's graph database.
  * What's [cayley](https://github.com/cayleygraph/cayley).
  * How cayley actually store data.
  * What's N-Triples.
  * What's N-Quads.
  * What's RDF.
  * What's blank node.
  * How cayley deal with 'better be an object field', like the below 'mobilePhone':

    ```
    realName: 'XXX_L3',
    mobilePhone: {
      isVerified: false,
      alpha3CountryCode: '+65',
      mobilePhoneNoWithCountryCallingCode: '+6586720011'
    }

    </user/shortid/23TplPdS> <mobilePhone> _:l8 "companyA" .
    _:l8 <isVerified> "false" "companyA" .
    _:l8 <alpha3CountryCode> "+65" "companyA" .
    _:l8 <mobilePhoneNoWithCountryCallingCode> "+6586720011" "companyA" .
    ...
    ```
  * How to design your data schema for cayley.

  * Resource list
    * [Look at Cayley](http://blog.thefrontiergroup.com.au/2014/10/look-cayley/)
    * [Neo4j](http://info.neo4j.com/rs/neotechnology/images/Graph_Databases_2e_Neo4j.pdf)
    * 
    * [N-Triples](https://en.wikipedia.org/wiki/N-Triples)
    * [Blank Node](https://en.wikipedia.org/wiki/Blank_node)
    * 
    * [JSON-LD](http://json-ld.org/learn.html)
    * [JSON-LD](http://json-ld.org/spec/latest/json-ld-rdf/)
    * [JSON-LD](https://www.w3.org/TR/json-ld/)
    * [JSON-LD](https://www.w3.org/TR/json-ld-api/)
    * 
    * [!!! Beginners guide to schema design](https://discourse.cayley.io/t/beginners-guide-to-schema-design-working-thread/436)
    * [!!! Beginners guide to schema design](https://github.com/tamethecomplex/tutorial-documents/blob/master/cayley/BeginnersGraphDatabaseSchemaDesign.md)
    * [!!! RDF schema](https://www.w3.org/TR/rdf-schema/)
    * [Beginners guide to schema design](https://discourse.cayley.io/t/best-place-for-a-beginner-to-learn-about-schema-design/432)
    * 
    * [Update a quad](https://discourse.cayley.io/t/code-review-how-to-update-a-quad/336/7)
    * [RDF](https://discourse.cayley.io/t/how-to-improve-my-data-and-explain-the-topic-of-rdf/345/9)


