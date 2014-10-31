# get-flow

Node.js Flow Control Utility, easy to use and ready for production.

## Installation

```bash
$ npm install get-flow
```

## Coding

Function **_runSeries(taskList, callback, ...)_** runs an `Array of tasks` (you can mix synchronous and asynchronous tasks)
in `series`, calling back when done or immediately if an exception occurs.

 - Each task must be a `Function` and all the ASYNC tasks **must have the first argument named `callback`**
in order to be recognised as asynchronous task.

- Synchronous task will be internally executed surrounded by a try/catch statement,
any exception stops the flow and it will be passed to the main callback as first argument.

- ASynchronous task **must manage any exception internally and propagate it to callback as first argument**

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
  
## Unit Testing 

```bash
$ npm test
```

## License

The project released under the [Simplified BSD license](./LICENSE) 
  
  


