# @grafctory/http

[![npm](https://img.shields.io/npm/v/@grafctory/http)](https://www.npmjs.com/package/@grafctory/http)

Composable middleware for the standard fetch API.

**@grafctory/http** layers recurring HTTP concerns over the standard
[`fetch`](https://developer.mozilla.org/docs/Web/API/Window/fetch) function, without introducing a bespoke client API. A
middleware wraps a fetch implementation, returning a new one with the same signature that adjusts requests and responses
as they flow through, and a chain of them is assembled into a single client to be shared across an application:

- **Drop-in Clients**: every client is a standard `fetch` function, interchangeable with the platform primitive
- **Bundled Middlewares**: recurring concerns come solved and tested, keeping call sites free of boilerplate
- **Open Customisation**: your own middlewares are written and layered exactly like the bundled ones

# Installation

```shell
npm install @grafctory/http
```

> [!WARNING]
>
> TypeScript consumers must use `"moduleResolution": "nodenext"/"node16"/"bundler"` in `tsconfig.json`.
> The legacy `"node"` resolver is not supported.

# Usage

A client is assembled once from the middlewares an application needs and shared across it, with the root module
supplying the assembler and each subpath module a middleware:

```typescript
import { createFetch } from "@grafctory/http";
import { bearer } from "@grafctory/http/bearer";
import { headers } from "@grafctory/http/headers";
import { success } from "@grafctory/http/success";
import { timeout } from "@grafctory/http/timeout";

const client = createFetch(                // shared across the application
	bearer(() => session.token),             // Authorization header, resolved per request
	headers({ Accept: "application/json" }), // stated on every request
	timeout(5000),                           // gives up on a server that never replies
	success()                                // rejects unless response.ok
);

const response = await client("https://api.example.com/data");
```

Middlewares are layered in declaration order: requests are processed by the first middleware first and reach the
standard `fetch` function last, while responses travel back through the chain in reverse. The resulting client is itself
a standard `fetch` function, so it is handed to anything expecting the platform primitive and is itself wrapped again by
a further chain.

> [!NOTE]
>
> This section introduces the overall organisation; each module documents its own concern in detail in the API
> reference:
>
> | Module                     | Description                        |
> |----------------------------|------------------------------------|
> | [@grafctory/http]           | Client assembly and HTTP utilities |
> | **Request Authentication** |                                    |
> | [@grafctory/http/basic]     | `Basic` authentication             |
> | [@grafctory/http/bearer]    | `Bearer` authentication            |
> | **Exchange Control**       |                                    |
> | [@grafctory/http/headers]   | Default header fields              |
> | [@grafctory/http/throttle]  | Adaptive pacing and retries        |
> | [@grafctory/http/timeout]   | Bounded response wait              |
> | [@grafctory/http/success]   | Uniform failure reporting          |
> | [@grafctory/http/monitor]   | Exchange reporting                 |
> | [@grafctory/http/xsrf]      | XSRF protection                    |
> | **Exchange Resolution**    |                                    |
> | [@grafctory/http/cache]     | HTTP response caching              |
> | [@grafctory/http/protocol]  | Custom protocol handlers           |
> | [@grafctory/http/transport] | Custom fetch transport             |
> | [@grafctory/http/mock]      | Mock fetch transport               |

[@grafctory/http]: https://grafctory.github.io/http/modules/index.html

[@grafctory/http/basic]: https://grafctory.github.io/http/modules/basic.html

[@grafctory/http/bearer]: https://grafctory.github.io/http/modules/bearer.html

[@grafctory/http/headers]: https://grafctory.github.io/http/modules/headers.html

[@grafctory/http/throttle]: https://grafctory.github.io/http/modules/throttle.html

[@grafctory/http/timeout]: https://grafctory.github.io/http/modules/timeout.html

[@grafctory/http/success]: https://grafctory.github.io/http/modules/success.html

[@grafctory/http/monitor]: https://grafctory.github.io/http/modules/monitor.html

[@grafctory/http/xsrf]: https://grafctory.github.io/http/modules/xsrf.html

[@grafctory/http/cache]: https://grafctory.github.io/http/modules/cache.html

[@grafctory/http/protocol]: https://grafctory.github.io/http/modules/protocol.html

[@grafctory/http/transport]: https://grafctory.github.io/http/modules/transport.html

[@grafctory/http/mock]: https://grafctory.github.io/http/modules/mock.html

# Support

- open an [issue](https://github.com/grafctory/http/issues) to report a problem or to suggest a new feature
- start a [discussion](https://github.com/grafctory/http/discussions) to ask a how-to question or to share an idea

# License

This project is licensed under the Apache 2.0 License –
see [LICENSE](https://github.com/grafctory/http?tab=Apache-2.0-1-ov-file) file for details.
