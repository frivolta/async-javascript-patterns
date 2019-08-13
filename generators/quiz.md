# Question 1

Create a custom async generator that loops over the files that are passed in.

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

function* fileLoader(files) {...}

(async () => {
  for await (let contents of fileLoader([
    "./files/demofile.txt",
    "./files/demofile.other.txt"
  ])) {
    console.log(contents);
  }
})();
```

## Answer

```js
const fs = require('fs');

const fileLoader = ['./files/demofile.txt', './files/demofile.other.txt'];
//Making a promise version of readfile for training purposes
function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

// STEP 1: Making a promise all for files with standard promises
/* let promises = fileLoader.map(file => readFile(file, 'utf8'));
Promise.all(promises)
  .then(data => {
    console.log(data);
  })
  .catch(err => console.log(err)); */

//STEP 2: Making a promise all for files with async await
/* (async () => {
  for (let file of fileLoader) {
    let value = await readFile(file, 'utf8');
    console.log(value);
  }
})(); */

//STEP 3: Resolve promises using generators
function* fileGenerator(files) {
  for (let file of files) {
    yield Promise.resolve(readFile(file, 'utf8'));
  }
}

(async () => {
  for await (let x of fileGenerator(fileLoader)) {
    console.log(x);
  }
})();
```
