# Using the SDK

This file will not be public, but it will be used to write the final documentation. No need for fancy explanations, but make sure to include examples and suffucient details for writing the official doc off of it. 

You can add or remove sections as needed.

## Installation
TODO: Add instructions on how to install the SDK in a project, like running a command or updating a file.

eg.
```bash
npm install tggl
```

## Setup
TODO: Add instructions on how to setup the SDK in a project.

eg.

Instanciate a singleton client
```typescript
import { TgglClient } from 'tggl-client'
 
const client = new TgglClient('YOUR_API_KEY')
```
## Usage
TODO: Add instructions on how to use the SDK in a project, with code examples and gotchas if needed.

eg.

Stateful:
```typescript
await client.setContext({
  userId: 'foo',
  email: 'foo@gmail.com',
  country: 'FR',
  // ...
})
 
if (client.isActive('my-feature')) {
  // ...
}
 
if (client.get('my-feature') === 'Variation A') {
  // ...
}
```

Stateless:
```typescript
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

## Polling
TODO: Explain how polling works or remove this section if it does not make sense in this language

eg. 
```typescript
// Update flags every 5 seconds
const client = new TgglClient('YOUR_API_KEY', {
  pollingInterval: 5000
})

client.startPolling(8000) // Start polling every 8 seconds
client.startPolling(3000) // Change frequency to every 3 seconds
client.stopPolling() // Stop polling

const stopListener = client.onResultChange((flags) => {
  // Some flag has changed
  // Either check the flags object or use isActive/get on the client
})

// Call stopListener() to remove the listener
```

## Local evaluation
TODO: Explain how local evaluation works

eg.

```typescript
import { TgglLocalClient } from 'tggl-client'
 
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
