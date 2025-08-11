### Promise Chaining

Promise chaining is a mechanism in JavaScript that allows you to execute a sequence of asynchronous operations in a specific order. It works by "chaining" `.then()` calls to a promise. Each `.then()` call returns a new promise, which can then be chained with another `.then()` call.

Here's a breakdown of how it works:

1. **A function returns a promise:** An asynchronous function, like one making an API call, returns a promise. This promise represents the eventual completion or failure of the operation.

2. **The first `.then()`:** You attach a `.then()` method to this promise. This method takes a callback function that will execute when the promise is fulfilled (resolved). The value passed to this callback is the resolved value of the promise.

3. **Returning a value or another promise:**

      * If you return a **value** from the `.then()` callback, the next `.then()` in the chain will receive that value as its resolved value.
      * If you return **another promise** from the `.then()` callback, the next `.then()` will wait for that new promise to resolve before executing. This is the core of promise chaining, as it allows you to sequence asynchronous operations.

4. **The `.catch()`:** At the end of the chain, you can add a `.catch()` method. This is where you handle any errors that occur in any of the preceding promises in the chain. If a promise in the chain is rejected, the chain will skip all subsequent `.then()` calls and jump directly to the `.catch()` block.

**Example of Promise Chaining:**

```javascript
// Function that returns a promise
function fetchUserData(userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (userId === 123) {
        resolve({ id: 123, name: 'Alice' });
      } else {
        reject('User not found');
      }
    }, 1000);
  });
}

function fetchUserPosts(userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (userId === 123) {
        resolve(['Post 1', 'Post 2', 'Post 3']);
      } else {
        reject('Posts not found');
      }
    }, 1000);
  });
}

// Chaining the promises
fetchUserData(123)
  .then(user => {
    console.log('User data fetched:', user);
    // Now, we return another promise to fetch the posts
    return fetchUserPosts(user.id);
  })
  .then(posts => {
    console.log('User posts fetched:', posts);
    // You can continue to chain here
    return 'All done!';
  })
  .then(message => {
    console.log(message);
  })
  .catch(error => {
    console.error('An error occurred:', error);
  });

```

### Different Types of Promises

While there's only one core `Promise` object in JavaScript, there are several "types" of promises based on how they are created and their purpose. These are often used with `Promise.all()`, `Promise.race()`, etc., which are static methods on the `Promise` constructor.

#### 1\. Resolved Promise

A resolved promise is one that has successfully completed and has a value. You can explicitly create a resolved promise using `Promise.resolve()`. This is useful for functions that might sometimes return a promise and sometimes return a synchronous value.

```javascript
const resolvedPromise = Promise.resolve('Hello World');

resolvedPromise.then(value => {
  console.log(value); // Output: Hello World
});
```

#### 2\. Rejected Promise

A rejected promise is one that has failed, usually with an error. You can create a rejected promise using `Promise.reject()`. This is useful for simulating errors or for functions that need to return a rejected promise immediately.

```javascript
const rejectedPromise = Promise.reject(new Error('Something went wrong'));

rejectedPromise.catch(error => {
  console.error(error.message); // Output: Something went wrong
});
```

#### 3\. Pending Promise

A pending promise is in its initial state. It is neither resolved nor rejected. This is the state of a promise from the time it is created until the `resolve()` or `reject()` function is called.

```javascript
const pendingPromise = new Promise((resolve, reject) => {
  // The promise is pending here...
  setTimeout(() => {
    resolve('Pending promise is now resolved!');
  }, 2000);
});

// The promise is pending for 2 seconds
pendingPromise.then(value => {
  console.log(value);
});
```

### Static Methods for Handling Multiple Promises

JavaScript's `Promise` object also provides several static methods to handle collections of promises.

#### 4\. `Promise.all()`

`Promise.all()` takes an array of promises and returns a single promise. This single promise resolves with an array of the resolved values of the input promises, but only when **all** of the input promises have resolved. If any of the promises in the input array are rejected, the `Promise.all()` promise is immediately rejected with the error of the first promise that failed.

```javascript
const promise1 = Promise.resolve(3);
const promise2 = 42; // A non-promise value is treated as a resolved promise
const promise3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 'foo');
});

Promise.all([promise1, promise2, promise3]).then(values => {
  console.log(values); // Output: [3, 42, 'foo']
});
```

#### 5\. `Promise.race()`

`Promise.race()` also takes an array of promises and returns a single promise. This promise resolves or rejects as soon as **any** of the input promises resolves or rejects, with the value or error from that first promise. It's useful for scenarios where you want to perform an action as soon as the fastest promise completes.

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(resolve, 500, 'one');
});

const promise2 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 'two');
});

Promise.race([promise1, promise2]).then(value => {
  console.log(value); // Output: 'two' (since it resolves faster)
});
```

#### 6\. `Promise.allSettled()`

`Promise.allSettled()` takes an array of promises and returns a single promise. This promise resolves when **all** of the input promises have either resolved or been rejected. The resolved value is an array of objects, each describing the outcome of each promise (e.g., `{ status: 'fulfilled', value: ... }` or `{ status: 'rejected', reason: ... }`). This is useful when you don't want the entire operation to fail if one promise fails.

```javascript
const promise1 = Promise.resolve(3);
const promise2 = new Promise((resolve, reject) => setTimeout(reject, 100, 'error'));

Promise.allSettled([promise1, promise2]).then(results => {
  console.log(results);
  // Output:
  // [
  //   { status: 'fulfilled', value: 3 },
  //   { status: 'rejected', reason: 'error' }
  // ]
});
```

#### 7\. `Promise.any()`

`Promise.any()` takes an array of promises and returns a single promise. This promise resolves as soon as **any** of the input promises resolves, with the value of that promise. It only rejects if all of the input promises are rejected. This is useful when you need at least one promise to succeed.

```javascript
const promise1 = Promise.reject('Error A');
const promise2 = Promise.reject('Error B');
const promise3 = new Promise((resolve, reject) => setTimeout(resolve, 100, 'Success!'));

Promise.any([promise1, promise2, promise3]).then(value => {
  console.log(value); // Output: 'Success!'
});
```
