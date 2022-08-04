# Unit Testing Summary

## Table of Contents

- [Asynchronous Tests](#async_testing)

## Async Testing <a name="async_testing"></a>

- #### Callback functions

<sub>async-example.js</sub>

```
export function generateToken(userEmail, doneFn) {
  jwt.sign({ email: userEmail }, 'secret123', doneFn);
}
```

- testing async code that yields callback functions
- The **Vitest** wouldn't wait for the callback function to be executed, so the assertion wouldn't happen and the test always will be passed,
- **done** CB function is used in case of the expect method is called inside a callback function
- calling done after the assertion will make **Vitest** wait for the callback function to be executed

<sub>async-example.test.js</sub>

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
