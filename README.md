Sensors Analytics  [![NPM version][npm-image]][npm-url] [![Build Status][ci-image]][ci-url] [![Dependency Status][depstat-image]][depstat-url]
==============================

> This is the home-brewed version of Node SDK for [Sensors Analytics].

## Install

Install using [npm][npm-url].

    $ npm install sa-sdk-node --save

## Usage

### Basic Usage

```js
import SensorsAnalytics from 'sa-sdk-node'

const sa = new SensorsAnalytics()

sa.submitTo('http://xxx.cloud.sensorsdata.cn:8006/sa?token=xxx')

// Super Properties that assigned to every event tracking
sa.registerSuperProperties({ $appVersion: '1.0.0', env: 'production' })

// Track event
sa.track('user-id', 'userHappy')

// Track event with custom properties
sa.track('user-id', 'newOrder', { orderId: '12323' })

// Track event with specific time
sa.track('user-id', "newOrder", { orderId: '123234', '$time': new Date('2016-03-12T05:24:19.894+08:00') })


// Track Signup
sa.trackSignup('device-id', 'user-id')

// Manipuate user project
sa.profileSet('user-id', { age: 18 })
sa.profileSetOnce('user-id', { registerTime: new Date().valueOf() })
sa.profileIncrement('user-id', { scoreCount: 100, issueCount: -1 })
sa.profileAppend('user-id', { tags: ['student', 'developer'] })
sa.profileUnset('user-id', ['temporaryTag'])

```

For more detailed information about each api, checkout [Sensors Analytics manual]

### Config Submitter

```js
import SensorsAnalytics from 'sa-sdk-node'

const url = 'http://xxx.cloud.sensorsdata.cn:8006/sa?token=xxx'

const sa = new SensorsAnalytics()

const submitter = sa.submitTo(url, { gzip: true, mode: 'track', timeout: 10 * 1000 })
// gzip: whether enable gzip, default to true
// mode: could be 'track' (production use), 'debug' (diagnosis data), 'dryRun' (diagnosis with no data recorded), default to track
// timeout: Http timeout in ms, default to 10s
```

Submitter can be create manually and attach to `SensorsAnalytics` manually

```js
import SensorsAnalytics, { Submitter } from 'sa-sdk-node'

const url = 'http://xxx.cloud.sensorsdata.cn:8006/sa?token=xxx'

const sa = new SensorsAnalytics()

const submitter = new Submitter(url, { gzip: true, mode: 'track', timeout: 10 * 1000 })

sa.subscribe(submitter)
```

Network error handling

```js
submitter.catch((err) => console.error(err))
```

### Advanced Filtering

Suppose

```js
import SensorsAnalytics, { Submitter } from 'sa-sdk-node'

const url = 'http://xxx.cloud.sensorsdata.cn:8006/sa?token=xxx'

const sa = new SensorsAnalytics()

const submitter = new Submitter(url, { gzip: true, mode: 'track', timeout: 10 * 1000 })

```

### Batch Mode

```js
// Event will be submitted as 20 in a group
sa.windowWithCount(20)
  .subscribe(submitter)
```

### Batch or Timeout
```js
// Event will be submitted as 20 in a group or also will be submitted 5s passed from the first event
sa.windowWithTimeOrCount(5000, 20)
  .subscribe(submitter)
```

### Filtering
```js
// All the event that raised by debug build app won't be submitted
sa.filter((event) => event.properties.releaseType !== 'debug')
  .subscribe(submitter)
```

### Debounce
```js
// Useful while tracking user input or other case
// The event won't be tracked unless user has stopped typing for 500ms
sa.debounce(500)
  .subscribe(submitter)

textInput.onChange((text) => sa.track(userId, 'userType', { text }))
```

### RxJS

This library is powered by Microsoft's [RxJS].

`SensorsAnalytics` is an [Observable], which yields tracking data
`Submitter` is an [Observer], which consume the tracking data

`Submitter` is also an [Observable], which yields `Error` when network errors.

For more detail, checkout Microsoft's [Rx documentation]

## License
MIT

[![NPM downloads][npm-downloads]][npm-url]

[homepage]: https://github.com/timnew/sa-sdk-node

[npm-url]: https://npmjs.org/package/sa-sdk-node
[npm-image]: http://img.shields.io/npm/v/sa-sdk-node.svg?style=flat
[npm-downloads]: http://img.shields.io/npm/dm/sa-sdk-node.svg?style=flat

[ci-url]: https://travis-ci.org/timnew/sa-sdk-node/
[ci-image]: https://img.shields.io/travis/timnew/sa-sdk-node.svg?style=flat

[depstat-url]: https://gemnasium.com/timnew/sa-sdk-node
[depstat-image]: http://img.shields.io/gemnasium/timnew/sa-sdk-node.svg?style=flat

[Sensors Analytics]: http://sensorsdata.cn/
[Sensors Analytics manual]: http://sensorsdata.cn/manual/index.html
[RxJS]: https://github.com/Reactive-Extensions/RxJS
[Observable]: https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api/core/observable.md
[Observer]: https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api/core/observer.md
[Rx documentation]: https://github.com/Reactive-Extensions/RxJS/tree/master/doc
