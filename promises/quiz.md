# Question 1

Create a promise version of the async readFile function

```js
const fs = require("fs");

function readFile(filename, encoding) {
  fs.readFile(filename, encoding, (err, data) => {
    //TODO
  });
}
readFile("./files/demofile.txt", "utf-8")
    .then(...)
});
```

## Answer

```js
const fs = require('fs');

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile('./files/demofile.tx', 'utf-8')
  .then(data => console.log(data))
  .catch(err => console.log('Error: ', err));
```

---

# Question 2 - 3 - 4

Load a file from disk using readFile and then compress it using the async zlib node library, use a promise chain to process this work.

## Answer

```js
const fs = require('fs');
const zlib = require('zlib');

function zlibPromise(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (error, result) => {
      if (error) reject(error);
      resolve(result);
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile('./files/demofile.txt', 'utf-8')
  .then(data => zlibPromise(data))
  .then(res => console.log(res))
  .catch(err => console.log(err));
```

---

# Question 5

Create some code that tries to read from disk a file and times out if it takes longer than 1 seconds, use `Promise.race`

```js
function readFileFake(sleep) {
  return new Promise(resolve => setTimeout(resolve, sleep));
}

readFileFake(5000); // This resolves a promise after 5 seconds, pretend it's a large file being read from disk
```

## Answer

```js
function readFileFake(sleep) {
  return new Promise(resolve => setTimeout(resolve, sleep, 'read'));
}

function timeout(sleep) {
  return new Promise((_, reject) => setTimeout(reject, sleep, 'timeout'));
}

Promise.race([readFileFake(5000), timeout(1000)])
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

---

# Question 6

Create a process flow which publishes a file from a server, then realises the user needs to login, then makes a login request, the whole chain should error out if it takes longer than 1 seconds. Use `catch` to handle errors and timeouts.

## Answer

```js
function authenticate() {
  console.log('Authenticating');
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 200 }));
}

function publish() {
  console.log('Publishing');
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 403 }));
}

function timeout(sleep) {
  return new Promise((resolve, reject) => setTimeout(reject, sleep, 'timeout'));
}

Promise.race([publish(), timeout(1000)])
  .then(res => {
    if (res.status === 403) {
      return authenticate();
    }
    return res;
  })
  .then(res => {
    // Process save responce
    console.log('Published');
  })
  .catch(err => {
    if (err === 'timeout') {
      console.error('Request timed out');
    } else {
      console.error(err);
    }
  });
```

Alternative answer with safePublish returning a publish promise

```js
function authenticate() {
  console.log('Authenticating');
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 200 }));
}

function publish() {
  console.log('Publishing');
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 403 }));
}

function timeout(sleep) {
  return new Promise((resolve, reject) => setTimeout(reject, sleep, 'timeout'));
}

function safePublish() {
  return publish().then(res => {
    if (res.status === 403) {
      return authenticate();
    }
    return res;
  });
}

Promise.race([safePublish(), timeout(1000)])
  .then(res => {
    // Process save responce
    console.log('Published');
  })
  .catch(err => {
    if (err === 'timeout') {
      console.error('Request timed out');
    } else {
      console.error(err);
    }
  });
```
