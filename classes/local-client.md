# The LocalClient class
## Usage
The local client is used to make an API call to Tggl only once to get the configuration and then evaluate many contexts locally with no API call required.

```typescript
const client = new TgglLocalClient('YOUR_SERVER_API_KEY')
 
// This method performs an API call and updates the flags configuration
await client.fetchConfig()
 
// Evaluation is performed locally
client.isActive({ userId: 'foo' }, 'my-feature')
client.isActive({ userId: 'bar' }, 'my-feature')
 
// You can also get the value of a flag, with and without default value
client.get({ userId: 'baz' }, 'my-feature')
client.get({ userId: 'foobar' }, 'my-feature', 42)
```

For safety and performance, it should be possible to use caching:

```typescript
const cachedConfig = await loadConfig()
 
const client = new TgglLocalClient('YOUR_SERVER_API_KEY', {
  initialConfig: cachedConfig
})
 
// If your language supports polling, you can use a callback to update the configuration
client.onConfigChange(async (config) => {
  await saveConfig(config)
})

// If your language does not support polling (short lived like PHP), you can manually update the configuration
const config = await client.fetchConfig()
await saveConfig(config)
```

## Reporting
The LocalClient is responsible for reporting usage with a `Reporter` (as private member).

It should report usage of flags with the `reportFlag` and usage of contexts with the `reportContext` methods of the `Reporting` class:
- When isActive is called
- When get is called
- When getAllACtiveFlags is called (does not report flags then, only reports context)

## Polling
If your language is long-lived like Node.js, it should be able to poll the API to get the latest configuration. For short-lived languages like PHP, the configuration should be updated manually.

```typescript
// When you instanciate the client
const client = new TgglLocalClient('YOUR_API_KEY', {
  pollingInterval: 5000 // you can use seconds intead of ms if it makes more sense in your language
})

// Or after
client.startPolling(8000) // Start polling every 8 seconds
client.startPolling(3000) // Change frequency to every 3 seconds
client.stopPolling() // Stop polling
```

## API call documentation
The API is documented [here](https://tggl.io/developers/api-reference/get-flags-config).

## Reference JS implementation
- You can copy the JS implementation available [here](https://github.com/Tggl/js-tggl-client/blob/master/src/TgglLocalClient.ts).
- The JS evaluation core implementation available [here](https://github.com/Tggl/tggl-core/blob/main/src/index.ts).

Note that JS is a little weird because it has both null and undefined and uses undefined as "inactive" and null as "active" for its response, but most other SDK have a class or object with { active: boolean, value: any }.

## Tests
Implementing the core that evaluates flag is kind of tedious, but fairly easy with TDD.
Simply copy [this JSON file](../tests/get.json[standard_tests.json](..%2Ftests%2Fstandard_tests.json)) project and write a single test like this:
```typescript
import standardTests from './standard_tests.json'

for (const { name, flag, context, expected } of standardTests) {
  test(name, () => {
    expect(evalFlag(context, flag)).toBe(expected)
  })
}
```
