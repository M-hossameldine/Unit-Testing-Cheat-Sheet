# Unit Testing Summary

## Table of Contents

- [Asynchronous Tests](#async_testing)
  - [Testing Callback functions](callback_testing)
  - [Testing Promises and async await](promises_testing)

## Async Testing <a name="async_testing"></a>

### Callback Functions <a name="callback_testing"></a>

For the following snippet **generateToken** takes a userEmail and a callback function to be called later with two parameters error and token.

async-example.js

```
export function generateToken(userEmail, doneFn) {
  jwt.sign({ email: userEmail }, 'secret123', doneFn);
}
```

- testing async code that yields callback functions
- The **Vitest** wouldn't wait for the callback function to be executed, so the assertion wouldn't happen and the test always will be passed,
- **done** CB function is used in case of the expect method is called inside a callback function
- calling done after the assertion will make **Vitest** wait for the callback function to be executed

async-example.test.js

```
import { describe, it, expect } from 'vitest';

import { generateToken } from './async-example';

describe('generateToken()', () => {

  it('should generate a token value', (done) => {
    let userTestEmail = 'test@test.com';

    generateToken(userTestEmail, (err, token) => {
      try {
        expect(token).toBeDefined();
        // expect(token).toBe(2); // to test error case
        done();
      } catch (err) {
        // if the assertion is wrong then it throws an error but "done" prevents that error from happening so we use the try catch syntax
        done(err);
      }
    });
  });
});
```

### Promises and async await <a name="promises_testing"></a>

async-example.js

```
export function generateTokenPromise(userEmail) {
  const promise = new Promise((resolve, reject) => {
    jwt.sign({ email: userEmail }, 'secret123', (error, token) => {
      if (error) {
        reject(error);
      } else {
        resolve(token);
      }
    });
  });

  return promise;
}
```

```
import { describe, it, expect } from 'vitest';
import { generateTokenPromise } from './async-example';

// testing Async-Code that uses promises
describe('generateTokenPromise()', () => {
  // 1st way - using promises
  it('should generate a value token', () => {
    let userTestEmail = 'test@test.com';

    // using return will make sure that "vitest" waits for the promise to be resolved
    return expect(generateTokenPromise(userTestEmail)).resolves.toBeDefined();
  });

  // 2nd way - using async-await
  it('should generate a value token', async () => {
    let userTestEmail = 'test@test.com';

    let token = await generateTokenPromise(userTestEmail);

    expect(token).toBeDefined();
  });
});
```
