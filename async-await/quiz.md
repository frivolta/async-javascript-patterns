# Question 1

Convert the promise version of the multi-file loader over to using async/await

```js
const util = require('util');
const fs = require('fs');
const readFile = util.promisify(fs.readFile);

const files = ['./files/demofile.txt', './files/demofile.other.txt'];

let promises = files.map(name => readFile(name, { encoding: 'utf8' }));
Promise.all(promises).then(values => {
  // <-- Uses .all
  console.log(values);
});
```

## Answer 1

```js
const util = require('util');
const fs = require('fs');
const readFile = util.promisify(fs.readFile);

const files = ['./files/demofile.txt', './files/demofile.other.txt'];

(async () => {
  for (let file of files) {
    let value = await readFile(file, 'UTF-8');
    console.log(value);
  }
})();
```

## Answer 2

```js
const util = require('util');
const fs = require('fs');
const readFile = util.promisify(fs.readFile);

const files = ['./files/demofile.txt', './files/demofile.other.txt'];

(async () => {
  let promises = files.map(file => readFile(file, 'UTF-8'));
  let value = await Promise.all(promises);
  console.log(value);
})();
```

---

# Question 2

Again convert the promise version of the multi-file loader over to using async/await but using a custom async iterator with the following syntax

node --harmony-async-iteration <file.js>

```js
const util = require('util');
const fs = require('fs');
const readFile = util.promisify(fs.readFile);

const fileIterator = files => ({
  [Symbol.asyncIterator]: () => ({
    x: 0,
    next() {
      if (this.x >= files.length) {
        return {
          done: true
        };
      }
      let file = files[this.x++];
      return readFile(file, 'utf8').then(data => ({
        done: false,
        value: data
      }));
    }
  })
});

const files = ['./files/demofile.txt', './files/demofile.other.txt'];

(async () => {
  for await (let x of fileIterator(files)) {
    console.log(x);
  }
})();
```

## Answer
