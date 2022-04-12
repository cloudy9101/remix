`bin/next.ts dev`

### Dev Server
```
- packages/next/bin/next.ts dev
- packages/next/cli/next-dev.ts #nextDev
- packages/next/server/lib/start-server.ts #startServer -> Promise<NextServer>
  - create next app (it's an instance of NextServer)
  - app.getRequestHandler() -> create requestHandler
  - start http server by http.createServer((req, res) => requestHandler(req, res)), delegate req, res to app.requestHandler
  - server.listen(port, hostname)
- packages/next/server/next.ts -> defined NextServer class
  - getRequestHandler
  - getServerRequestHandler
  - getServer
  - createServer -> server (it's NextNodeServer 'packages/next/server/next-server.ts')
  - server.getRequestHandler
- packages/next/server/next-server.ts
  - getRequestHandler -> NodeRequestHandler
  - super.getRequestHandler (super is BaseServer 'packages/next/server/base-server.ts')
- packages/next/server/base-server.ts
  - getRequestHandler -> BaseRequestHandler
  - this.handleRequest(req, res, parsedUrl?)
  - this.run(req, res, parsedUrl?)
  - this.router.execute(req, res, parsedUrl?)
  - this.router = new Router(this.generateRoutes()) (Router 'packages/next/server/router.ts')
  - this.generateRoutes
    - headers -> routes-manifest.json
    - redirects -> routes-manifest.json
    ...
  - new Router()
  - router.execute(req, res, parsedUrl?)
- packages/next/server/router.ts
  - execute(req, res, parsedUrl?)
    - allRoutes is an array of Route object
    - loop of allRoutes as testRoute
    - if testRoute.match(currentPathname) then
      - result = testRoute.fn(req, res, newParams, parsedUrlUpdated)
      - return if result.finished
      ...
    - catchAllRoute -> base-server.ts#generateRoutes
- packages/next/server/base-server.ts
  - catchAllRoute
  - if pathname.startsWith('/api')
    - handled = handleApiRequest(req, res, parsedUrl)
      - runApi(req, res, query, params, page, builtPagePath) delegated to next-server.ts#runApi
      - packages/next/server/next-server.ts#runApi delegated to packages/next/server/api-utils/node.ts#apiResolver
      - packages/next/server/api-utils/node.ts#apiResolver
      - resolver = resolverModule (resolverModule = require(builtPagePath)) (builtPagePath is the path of the api handler)
      - await resolver(req, res)
    - return if handled
  - else
    - render
    - renderToResponse
    - findPageComponents delegated to packages/next/server/next-server.ts#findPageComponents
    - findPageComponents -> loadComponents -> require(pagePath)
    - renderToResponseWithComponent
    - doRender
    - renderHTML delegated to packages/next/server/next-server.ts#renderHTML
    - renderToHTML

```