# Tggl SDK specifications
## Overview
Tggl is a feature flag service. This document describes the Tggl SDK specifications. Here is a high level overview of what is expected:
- Native implementation in the target language
- Follow other SDKs in terms of naming and API while following the language conventions and best practices
- Implement tests that are already written for you
- Implement CI using GitHub actions to run those tests
- Publish the package to the language's package manager (if relevant for that language)
- Document the process of making changes, bumping the SDK version, and releasing the new version
- Document the process of installing and using the SDK in a project

## Set up the project
- Repository url: `https://github.com/Tggl/<language>-tggl-client`
- Homepage: `https://tggl.io/developers/sdks/<language>`
- Package name: ideally `tggl`, or `tggl-client` / `tggl/client` / `TgglClient` / `tggl_client` if `tggl` is already taken or the convention with that package manager requires it.

## Naming conventions
All naming here will us Javascript conventions, so mostly camelCase and PascalCase. Please adapt to the target language conventions.

If the target language supports namespaces, the package should be namespaced under `Tggl`, with classes named like `Client` or `Response`. Otherwise, classes can be prefixed like `TgglClient` or `TgglResponse`.

## Implementing classes
There are 4 classes to implement. We recommend implementing the classes in the following order as they depend on each other:
- [Reporting](./classes/reporting.md)
- [Response](./classes/response.md)
- [Client](./classes/client.md)
- [LocalClient](./classes/local-client.md)

## Writing documentation
No need to write fancy sentences. Those two files will not be public, but they will be used to write the final documentation. Make sure to include examples and sufficient details for writing the official doc off of it. 

Update those two files:
- `doc/contributing.md`
- `doc/using-the-sdk.md`
