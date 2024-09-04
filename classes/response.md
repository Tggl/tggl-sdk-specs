# The Response class
## Usage
The response class is a wrapper to the API response. It will be used like this:
```typescript
const apiJsonResponse = {
  'my-feature': true,
  'my-other-feature': 'value'
}

const response = new Response(apiJsonResponse)

response.isActive('my-feature') // boolean
response.get('my-feature') // any
response.get('my-feature', 'my default value') // any 
```

## Reporting
A Response is responsible for parsing the API response and reporting usage with a `Reporter` (as private member).

It should report usage of flags with the `reportFlag` method of the `Reporting` class:
- When isActive is called
- When get is called

## Response documentation
The parsing is documented [here](https://tggl.io/developers/api-reference/evaluate-flags#interpreting-the-response).

But it is dead simple:
- The response is a JSON object
- `isActive('my-feature')` just checks if the key `my-feature` is present in the object, regardless of its value (the value could even be false, it would still be active)
- `get('my-feature')` returns the value of the key `my-feature` if it is present, otherwise it returns `defaultValue` which is the optional second paramater of the function (null if not provided)

## Reference JS implementation
You can copy the JS implementation available [here](https://github.com/Tggl/js-tggl-client/blob/master/src/TgglResponse.ts).

You can copy the implementation and run the tests without thinking too much about it.

## Tests
Tests have already been written for you, you can do TDD if you want. Simply copy [this JSON file](../tests/get.json) and [this JSON file](../tests/isActive.json) in your project and write a single test like this:
```typescript
import getTests from './testData/get.json'
import isActiveTests from './testData/isActive.json'

// - name: a string, the name of the test you can use as the test description
// - response: the response to pass to the constructor
// - flag: a string, the flag to test
// - value: any, the expected value of the flag
// - defaultValue?: any, the default value to pass to get
for (const { name, response, value, defaultValue, flag } of getTests) {
  test('get ' + name, async () => {
    const r = new Response(response)

    // defaultValue could be undefinedm in that case do not pass a default value
    if (defaultValue !== undefined) {
      expect(r.get(flag, defaultValue) ?? null).toEqual(value)
    } else {
      expect(r.get(flag) ?? null).toEqual(value)
    }
  })
}

// - name: a string, the name of the test you can use as the test description
// - response: the response to pass to the constructor
// - flag: a string, the flag to test
// - active: boolean, the expected active state of the flag
for (const { name, flag, response, active } of isActiveTests) {
  test('isActive ' + name, async () => {
    const r = new Response(response)

    expect(r.isActive(flag)).toBe(active)
  })
}
```
