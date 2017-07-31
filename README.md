# zookeeper-cluster-client

[![NPM version][npm-image]][npm-url]
[![build status][travis-image]][travis-url]
[![Test coverage][codecov-image]][codecov-url]
[![David deps][david-image]][david-url]
[![Known Vulnerabilities][snyk-image]][snyk-url]
[![NPM download][download-image]][download-url]

[npm-image]: https://img.shields.io/npm/v/zookeeper-cluster-client.svg?style=flat-square
[npm-url]: https://npmjs.org/package/zookeeper-cluster-client
[travis-image]: https://img.shields.io/travis/node-modules/zookeeper-cluster-client.svg?style=flat-square
[travis-url]: https://travis-ci.org/node-modules/zookeeper-cluster-client
[codecov-image]: https://codecov.io/gh/node-modules/zookeeper-cluster-client/branch/master/graph/badge.svg
[codecov-url]: https://codecov.io/gh/node-modules/zookeeper-cluster-client
[david-image]: https://img.shields.io/david/node-modules/zookeeper-cluster-client.svg?style=flat-square
[david-url]: https://david-dm.org/node-modules/zookeeper-cluster-client
[snyk-image]: https://snyk.io/test/npm/zookeeper-cluster-client/badge.svg?style=flat-square
[snyk-url]: https://snyk.io/test/npm/zookeeper-cluster-client
[download-image]: https://img.shields.io/npm/dm/zookeeper-cluster-client.svg?style=flat-square
[download-url]: https://npmjs.org/package/zookeeper-cluster-client

Support [cluster-client](https://www.npmjs.com/package/cluster-client) process model on [node-zookeeper-client](https://www.npmjs.com/package/node-zookeeper-client).

## Install

```bash
npm i zookeeper-cluster-client --save
```

## Usage

1\. Create a node using given path:

```js
const zookeeper = require('zookeeper-cluster-client');
const client = zookeeper.createClient('localhost:2181');
const path = process.argv[2];

client.once('connected', () => {
  console.log('Connected to the server.');
  client.create(path, err => {
    if (err) {
      console.log('Failed to create node: %s due to: %s.', path, err);
    } else {
      console.log('Node: %s is successfully created.', path);
    }
    client.close();
  });
});

client.connect();
```

2\. List and watch the children of given node:

```js
const zookeeper = require('zookeeper-cluster-client');
const client = zookeeper.createClient('localhost:2181');
const path = process.argv[2];

function listChildren(client, path) {
  client.getChildren(
    path,
    event => {
      console.log('Got watcher event: %s', event);
      listChildren(client, path);
    },
    (err, children, stat) => {
      if (err) {
        console.log('Failed to list children of %s due to: %s.', path, err);
        return;
      }
      console.log('Children of %s are: %j.', path, children);
    }
  );
}

client.once('connected', () => {
  console.log('Connected to ZooKeeper.');
  listChildren(client, path);
});

client.connect();
```

## Support APIs

- [x] `createClient(connectionString, [options])`
- [x] `connect()`
- [x] `close()`: return promise
- [x] `create(path, [data], [acls], [mode], callback)`
- [x] `remove(path, [version], callback)`
- [x] `setData(path, data, [version], callback)`
- [x] `getACL(path, callback)`
- [x] `setACL(path, acls, [version], callback)`
- [x] `mkdirp(path, [data], [acls], [mode], callback)`
- [x] `exists(path, [watcher], callback)`
- [x] `getChildren(path, [watcher], callback)`
- [x] `getData(path, [watcher], callback)`
- [ ] `addAuthInfo(scheme, auth)`
- [x] `State getState()`
- [x] `Buffer getSessionId()`
- [x] `Buffer getSessionPassword()`
- [x] `Number getSessionTimeout()`
- [ ] `transaction()`

### Extends APIs

Provides some useful APIs beyond node-zookeeper-client.

- [x] `watch(path, listener)`

  ```js
  client.watch('/foo', (err, data, stat) => {
    if (err) {
      // handle error
      return;
    }
    console.log('data => %s', data.toString());
    console.log('stat => %s', stat);
  });
  ```

- [x] `watchChildren(path, listener)`

  ```js
  client.watchChildren('/foo', (err, children, stat) => {
    if (err) {
      // handle error
      return;
    }
    console.log('children => %j', children);
    console.log('stat => %s', stat);
  });
  ```

## License

[MIT](LICENSE.txt)
