# The Reporting class
## Usage
This class will be instantiated and used like a logger:
```typescript
const reporter = new Reporting()

reporter.reportFlag('my-feature', { 
  active: true, 
  value: 'Variation A', 
  default: 'Variation B',
})
reporter.reportFlag('my-other-feature', { 
  active: false, 
  value: null, 
  default: null 
})
reporter.reportContext({
  userId: 'foo',
  email: 'foo@gmail.com',
  country: 'FR',
})
```

Note that calling `reportFlag` and `reportContext` simple accumulates the data in memory.

## When is the report actually sent?
It depends on you language and platform.
- Short-lived processes (eg. PHP): the report is sent when the process ends, ideally automatically without requiring the user to explicitly call `sendReport`.
- Long-lived processes (eg. Node.js): a report is sent every 2 seconds (if not empty) or when the process ends.
- Hybrid: maybe the target language allows for background threads or other ways to send the report.

It is up to you to implement the best way to send the report based on your language, ideally without blocking the main thread (in the background) and without the user having to do anything manually.

## API call documentation
Everything you need to know about the API call that sends the report is documented [here](https://tggl.io/developers/api-reference/reporting).

## Reference JS implementation
You can copy the JS implementation available [here](https://github.com/Tggl/js-tggl-client/blob/master/src/TgglReporting.ts).

You can copy the implementation and run the tests without thinking too much about it. The most important part is to make sure the report is sent using a technique that makes sense for your language.

## Tests
Tests have already been written for you, you can do TDD if you want. Simply copy [this JSON file](../tests/reporting.json) in your project and write a single test like this:
```typescript
import reportingTests from './testData/reporting.json'

// - name: a string, the name of the test you can use as the test description
// - app?: a string to pass to the constructor
// - appPrefix?: a string to pass to the constructor
// - calls: an array of objects with a type key (flag or context) and the relevant data
// - result: the expected result of the report
for (const { name, app, appPrefix, calls, result } of reportingTests) {
  test(name, () => {
    // Instanciate the Reporting class with the app and appPrefix
    const reporting = new TgglReporting({ apiKey: 'API_KEY', app, appPrefix })
    // Mock the system time since it will be used in the report (123456789 in seconds since epoch)
    jest.setSystemTime(123456789000)

    // Loop through the calls and report them
    for (const call of calls as any[]) {
      // If the type is flag, call reportFlag
      // It will have those properties
      // - slug: a string, the slug of the flag
      // - active: a boolean, the active state of the flag
      // - value?: any, the value of the flag
      // - defaultValue?: any, the default value of the flag
      if (call.type === 'flag') {
        reporting.reportFlag(call.slug, {
          active: call.active,
          value: call.value,
          default: call.defaultValue,
        })
      }

      // If the type is context, call reportContext
      // It will have those properties:
      // - context: an object, the context to report
      if (call.type === 'context') {
        reporting.reportContext(call.context)
      }
    }

    // After calling all the "calls", you can trigger the report and assert the result
    expectReporting(result)
  })
}
```

Those tests should cover all the edge cases and make sure the report is sent correctly. Note that those tests do not check that the report is actually sent periodically, only that the data is accumulated correctly.

## `reportFlag` signature
You can choose whichever signature feels good for your language:
```typescript
function reportFlag(slug: string, active: boolean, value?: any, defaultValue?: any): void
function reportFlag(slug: string, options: { active: boolean, value?: any, defaultValue?: any }): void
function reportFlag(options: { slug: string, active: boolean, value?: any, defaultValue?: any }): void
```

Notice that `value` and `defaultValue` are optional (default to `null`), and could be of any type (null, string, float, int, object, array, or boolean, basically anything that is serializable to JSON).

Calling this function just accumulates the data in memory until the report is sent, and returns nothing.

## `reportContext` signature
```typescript
function reportContext(context: Record<string, any>): void
```
- `receivedProperties` sends all the received keys.
- `receivedValues` only sends the non-empty string values.
- `receivedValues` also sends non-empty string labels for keys that match together, one ends with id and the other with name (eg. `userId` and `userName`). If you copy the JS implementation that should just work.
