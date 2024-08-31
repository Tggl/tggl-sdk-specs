# The Client class
## Usage
The client is used to make an API call to Tggl with a context as the body, and to retrieve the list of all active flags.

### Stateful (frontend languages only)
For frontend languages, the client can be **stateful** (ignore this section for backend languages):
```typescript
const client = new TgglClient('YOUR_API_KEY')

await client.setContext({
  userId: 'foo',
  email: 'foo@gmail.com',
  country: 'FR',
  // ...
})

if (client.isActive('my-feature')) {
  // ...
}
```

To be stateful, the client simply inherits the `Response` class (you don't have to re-implement `isActive` and `get`) and keep the context as a private member. Because the client is staeful, it should be able to poll the api:

```typescript
// When you instanciate the client
const client = new TgglClient('YOUR_API_KEY', {
  pollingInterval: 5000 // you can use seconds intead of ms if it makes more sense in your language
})

// Or after
client.startPolling(8000) // Start polling every 8 seconds
client.startPolling(3000) // Change frequency to every 3 seconds
client.stopPolling() // Stop polling
```

### Stateless (frontend and backend languages)
If your language supports both stateful and stateless clients, note that it is the same class, you can use a stateful client to evaluate context in a stateless maner:
```typescript
// flags is actually a Response object
const flags = await client.evalContext({
  userId: 'foo',
  email: 'foo@gmail.com',
  country: 'FR',
  // ...
})
 
if (flags.isActive('my-feature')) {
  // ...
}
 
if (flags.get('my-feature') === 'Variation A') {
  // ...
}
```

It can also evaluate contexts in batches
```typescript
// Responses are returned in the same order
const [ fooFlags, barFlags ] = await client.evalContexts([
  { userId: 'foo' },
  { userId: 'bar' },
])
```

Under the hood, `evalContext` just calls `evalContexts` with a single context:
```typescript
async function evalContext(context: Context): Response {
    return evalContexts([context])[0]
}
```

## API call documentation
The API is documented [here](https://tggl.io/developers/api-reference/evaluate-flags).

## Reference JS implementation
You can copy the JS implementation available [here](https://github.com/Tggl/js-tggl-client/blob/master/src/TgglClient.ts).
