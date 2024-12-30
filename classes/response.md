# The Response class
## Usage
The response class is a wrapper to the API response. It will be used like this:
```typescript
const apiJsonResponse = {
  'my-feature': true,
  'my-other-feature': 'value'
}

const response = new Response(apiJsonResponse)

response.get('my-feature', 'my default value') // any 
```

The end user will most likely never have to instantiate the response itself, it will be done by the `Client` class. But we abstract this logic to its own class because it can be used in different ways (you will discover how later).

Some typed languages can have different variations of the getMethod:
```typescript
response.getBoolean('my-feature', false) 
response.getString('my-feature', 'foo') 
response.getNumber('my-feature', 42) 
//...
```

## Reporting
A Response is responsible for parsing the API response, but also for reporting usage with a `Reporter` (the reporter should be a private member of the response class).

It should report usage of flags with the `reportFlag` method of the `Reporting` class. When get is called, the `value` should be the value of the flag taking into account the default value provided, and the `defaultValue` should just be the default value.

## Response documentation
The parsing is documented [here](https://tggl.io/developers/api-reference/evaluate-flags#interpreting-the-response).

But it is dead simple:
- The response is a JSON object
- `get('my-feature', 'my default value)` returns the value of the key `my-feature` if it is present, otherwise it returns `defaultValue`.

## Reference JS implementation
You can copy the JS implementation available [here](https://github.com/Tggl/js-tggl-client/blob/master/src/TgglResponse.ts).

You can copy the implementation and run the tests without thinking too much about it.

## Tests
Tests have already been written for you, you can do TDD if you want. Simply copy [this JSON file](../tests/get.json) in your project and write a single test like this:
```typescript
import getTests from './testData/get.json'

// - name: a string, the name of the test you can use as the test description
// - response: the response to pass to the constructor
// - flag: a string, the flag to test
// - value: any, the expected value of the flag
// - defaultValue?: any, the default value to pass to get
for (const { name, response, value, defaultValue, flag } of getTests) {
  test('get ' + name, async () => {
    const r = new Response(response)

    expect(r.get(flag, defaultValue) ?? null).toEqual(value)
  })
}
```
