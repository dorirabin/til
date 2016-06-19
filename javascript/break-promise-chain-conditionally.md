# Breaking promise chain conditionally

## Attempt 1

Considering the following code, no matter what value is returned from func1, the 2nd-then block will be executed!

```javascript
function attemp1() {
  return new Promise((resolve, reject) => {
    func1()
      .then((flag) => {
        console.log('1st then');

        if (flag) {
          resolve();
        } else {
          return func2();
        }
      })
      .then(() => {
        console.log('2nd then');
        resolve();
      });
  })
}
```

## Attemp 2

Let's try nested block. The problem is when func2 throws an error, the outer-catch-block cannot catch the error (obvious but easy to make this mistake).

```javascript
function attemp2() {
  return new Promise((resolve, reject) => {
    func1()
      .then((flag) => {
        console.log('1st then');

        if (flag) {
          resolve();
        } else {
          func2()
            .then(() => {
              console.log('nested then');
              resolve();
            });
        }
      })
      .catch((err) => {
        console.log('catch');
        reject();
      });
  })
}
```

## Attempt 3

Ok. Compromise. Add a nested catch-block. Even throwing an error from nested catch-block, the outer-catch-block is not executed! Where does the error go?

```javascript
function attemp3() {
  return new Promise((resolve, reject) => {
    func1()
      .then((flag) => {
        console.log('1st then');

        if (flag) {
          resolve();
        } else {
          func2()
            .then(() => {
              console.log('nested then');
              resolve();
            })
            .catch((err) => {
              console.log('nested catch');
              throw err;
            });
        }
      })
      .catch((err) => {
        console.log('catch');
        reject(err);
      });
  })
}
```

But for the following code, the catch-block is executed.

```javascript

function another() {
  return new Promise((resolve, reject) => {
    func1()
      .then((flag) => {
        throw 'error';
      })
      .catch((err) => {
        console.log('catch');
        reject(err);
      })
    });
}
```

## Final solution

### Skip it with reject

Use "reject" to skip the 2nd-then block. Remind me the Win32 "ERROR_SUCCESS" error code.

```javascript
function skipIt() {
  return new Promise((resolve, reject) => {
    func1()
      .then((flag) => {
        console.log('1st then');

        if (flag) {
          reject({ returnCode: 0 });
        } else {
          return func2();
        }
      })
      .then(() => {
        console.log('2nd then');
        resolve();
      })
      .catch((err) => {
        if (err && err.returnCode === 0) {
          resolve();
        } else {
          console.log('catch');
          reject(err);  
        }
      });
  })
}
```

### Reject in nested catch-block

This solution is ok. But sometimes we want to have "single-point" to handle error.

```javascript
function rejectInNestedCatch() {
  return new Promise((resolve, reject) => {
    func1()
      .then((flag) => {
        console.log('1st then');

        if (flag) {
          resolve();
        } else {
          func2()
            .then(() => {
              console.log('nested then');
              resolve();
            })
            .catch((err) => {
              console.log('nested catch');
              reject(err);
            });
        }
      })
      .catch((err) => {
        console.log('catch');
        reject(err);
      });
  });
}
```

### Async-await

Async-await should be the cleanest solution. The ```async``` function also returns ```Promise```.

```javascript
async function asyncAwait() {
  try {
    const flag = await func1();
    console.log('1st then');

    if (flag) {
      /* do nothing */
    } else {
      await func2();
      console.log('2nd then');
    }
  } catch(err) {
    console.log('catch');
    throw err;
  }
}
```
