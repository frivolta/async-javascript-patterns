# Question 1

The below code errors when you run it.

Make it run without errors but you cannot change the location of the `let` statement, that has to stay at the end.

```js
function doAsyncTask(cb) {
  cb();
}
doAsyncTask(_ => console.log(message));

let message = "Callback Called";
```

## Answer
```js
function doAsyncTask(cb) {
  // cb();

  // setImmediate(() => {
  //   console.log("Async Task Calling Callback");
  //   cb();
  // });

  process.nextTick(() => {
    console.log("Async Task Calling Callback");
    cb();
  });
}

doAsyncTask(() => console.log(message));

let message = "Callback Called";
```

----

# Question 2

The below code swallows the error and doesn't pass it up the chain, make it pass the error up the stack using the next callback.

```js
const fs = require("fs");

function readFileThenDo(next) {
  fs.readFile("./blah.nofile", (err, data) => {
    next(data);
  });
}

readFileThenDo(data => {
  console.log(data);
});
```
## Answer

```js
const fs = require("fs");

function readFileThenDo(next) {
  fs.readFile("./blah.nofile", (err, data) => {
    if (err) {
      next(err);
    } else {
      next(null, data);
    }
  });
}

readFileThenDo((err, data) => {
  if (err) {
    console.error(err);
  } else {
    console.log(data);
  }
});
```

---
# Question 3

Instead of passing it up the stack throw it instead and try to catch it later on.

```js
const fs = require("fs");

function readFileThenDo(next) {
  fs.readFile("./blah.nofile", (err, data) => {
    if (err) throw err;
    next(data);
  });
}
// Hint use try..catch
readFileThenDo(data => {
  console.log(data);
});
```
## Answer

Or if the error is serious, you can throw the error as soon as you see it.

try..catch desn't work as you expect with callbacks, it only really works with synchronous code.

By the time the callback throws the error we have moved on from the try..catch, the throw happens in the root scope and will just cause the program to exit.

```js
const fs = require("fs");

function readFileThenDo(next) {
  fs.readFile("./blah.nofile", (err, data) => {
    if (err) throw err;
    next(null, data);
  });
}

try {
  readFileThenDo((_, data) => console.log(data));
} catch (err) {
  console.error(err);
}
```
