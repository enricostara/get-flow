# get-flow

Node.js Flow Control Utility, powerful and easy to use 

## Installation

```bash
$ git clone --branch=master git://github.com/enricostara/get-flow.git
$ cd get-flow
$ npm install
```

## Coding

Function **_runSeries(taskList, callback, ...)_** runs an `Array of tasks`  **- and yes, you can mix synchronous 
and asynchronous tasks! -**  in `series`, calling back when done or immediately if an exception occurs.

 - Each task must be a **sync or async** `Function` and all the **async tasks must have the first argument named `callback`**
in order to be recognised as asynchronous.

- `Synchronous task` will be internally executed surrounded by a try/catch statement,
any exception stops the flow and it will be passed to the main callback as first argument.

- `ASynchronous task` **must manage any exception internally and propagate it to the callback as first argument**

- The arguments `callback` and `tasks`  are mandatory (`callback` can be null but in place anyway),
the subsequent arguments (if any) **will be passed only to the first task** .

See the following example where [sync, async, sync] tasks are executed in series:

```js
var flow = require('get-flow');

function multiply(x, y) {
    return x * y;
}

function addMillis(callback, x) {  // for ASYNC task the first argument must be the callback and be named 'callback'
    var start = new Date().getTime();
    setTimeout(function () {
        var millis = new Date().getTime() - start;
        callback(null, x + millis);
    }, 100);
}

function sqrt(x) {
    return Math.sqrt(x);
}

flow.runSeries(
    [   // tasks series
        multiply, // sync
        addMillis, // async
        sqrt  // sync again
    ],
    function(ex, result) {    // a proper callback when the last task (sqrt) is done
        console.log('no exception, (ex == null) = %s ', ex == null);
        console.log('result = %s', result);
        var assert = require('assert');
        assert.equal(result >= 10, true);
    },
    2, 3 // x and y passed at first task (multiply)
);

```

Function **_retryUntilIsDone(callback, retriesLimit, task, ...)_** calls the given `async task` until succeeds or the retries limit 
has been reached (the **default is 10 attempts**)

All the arguments are mandatory (`callback` and `retriesLimit` can be null but in place anyway),
the subsequent arguments (if any) will be passed to the task.

See the following example

```js
var flow = require('get-flow');

var i = 0;

function task(callback, input) {
    setTimeout(function () {
        i++;
        if (i === 5) {
            callback(null, input + i);
        } else {
            callback('err')
        }
    }, 10);
}

flow.retryUntilIsDone(
    function (ex, result) { // a proper callback
        console.log('no exception, (ex == null) = %s ', ex == null);
        console.log('result = %s', result);
        var assert = require('assert');
        assert.equal(result, 15);
    },
    null, // retries limit will be the default = 10
    task, // the async task
    10  // argument for the task
);

```

## Unit Testing 

```bash
$ npm test
```

## Dependencies

- [get-log](https://github.com/enricostara/get-log): a Node.js Logging Utility, easy to use and ready for production.
 

## License

The project is released under the [MIT license](./LICENSE) 
  