# The Client class
## Usage
The client is used to make an API call to Tggl with a context as the body, and to retrieve the list of all active flags. There is a stateful and a stateless way to use the client.

### Stateful (frontend languages only)
For frontend languages, the client can be **stateful** (ignore this section for backend languages):
```typescript
const client = new TgglClient('YOUR_API_KEY')

// Set the internal state of the client, and make an API call
await client.setContext({
  userId: 'foo',
  email: 'foo@gmail.com',
  country: 'FR',
  // ...
})

// Check the internal state of the client (no API call)
if (client.isActive('my-feature')) {
  // ...
}
```

To be stateful, the client simply inherits the `Response` class (you don't have to re-implement `get`) and keep the context as a private member. Because the client is stateful, it should be able to poll the api:

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

Polling means that an API call is made every N seconds in the background to keep the internal state up to date. Depending on the language and technology, it should be possible to subscribe to the client to get notified when the state changes.

### Stateless (frontend and backend languages)
Explicitly perform an API call to evaluate the context and get the result without modifying the internal state of the client.

If your language supports both stateful and stateless modes, we do not create two classes, you can use a stateful client to evaluate context in a stateless manner:
```typescript
// flags is actually a Response object
const flags = await client.evalContext({
  userId: 'foo',
  email: 'foo@gmail.com',
  country: 'FR',
  // ...
})
 
if (flags.get('my-feature', 'Variation B') === 'Variation A') {
  // ...
}

// You can evaluate other contexts
const barFlags = await client.evalContext({ userId: 'bar' })
const bazFlags = await client.evalContext({ userId: 'baz' })

// You could still use the client in a stateful way
await client.setContext({ userId: 'foo' })
```

It can also evaluate contexts in batches
```typescript
// Responses are returned in the same order, a single API call is performed (see API doc)
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

The stateless client has no polling mechanism, and no subscription mechanism.

## API call documentation
The API is documented [here](https://tggl.io/developers/api-reference/evaluate-flags).

## Reference JS implementation
You can copy the JS implementation available [here](https://github.com/Tggl/js-tggl-client/blob/master/src/TgglClient.ts).
