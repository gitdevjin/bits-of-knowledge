how to test `console.log`

```javascript
const nock = require("nock");
const { processUri } = require("./resource");

const originalConsoleLogFn = global.console.log;
const originalConsoleErrorFn = global.console.error;

describe("processUri tests", () => {
  let logOutput = null;
  let errorOutput = null;

  function testLogFn(...args) {
    logOutput = logOutput || [];
    args.forEach((arg) => logOutput.push(arg));
  }

  function testErrorFn(...args) {
    errorOutput = errorOutput || [];
    args.forEach((arg) => errorOutput.push(arg));
  }

  function finalize(output) {
    if (output && Array.isArray(output)) {
      return output.join("");
    }
    return output;
  }

  beforeEach(() => {
    global.console.log = testLogFn;
    global.console.error = testErrorFn;

    logOutput = null;
    errorOutput = null;
  });

  afterEach(() => {
    global.console.log = originalConsoleLogFn;
    global.console.error = originalConsoleErrorFn;

    logOutput = null;
    errorOutput = null;
  });

  test("passing file path works", async () => {
    await processUri("test/small-sample.html");

    const expected = `<html>
  <p>Hello World</p>
</html>
`;

    expect(finalize(logOutput)).toEqual(expected);
    expect(finalize(errorOutput)).toBe(null);
  });
});
```