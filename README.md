# ServiceFabric-StatelessWebAPI

Azure Service Fabric is a distributed systems platform provided by Microsoft for building and managing scalable and reliable applications. It enables developers to build microservices-based applications that can be easily deployed, scaled, and managed in the Azure cloud.

In Azure Service Fabric, a stateless service is a service that does not maintain any state locally. It processes requests and provides responses without relying on local state information. This makes stateless services highly scalable and resilient because they can be easily replicated and load balanced across multiple instances.

A stateless service in Azure Service Fabric can expose an API for receiving and processing requests. The API can be implemented using any programming language or framework supported by Azure Service Fabric. In this case, we'll provide a sample code in C# using Visual Studio.

Here's an example of a simple stateless service API in Azure Service Fabric:

Open Visual Studio and create a new Azure Service Fabric application project.
Add a stateless service to the project.
In the stateless service implementation file (e.g., MyStatelessService.cs), add the following code:

```typescript
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using System.Web.Http;
using System.Web.Http.SelfHost;

namespace MyStatelessService
{
    internal sealed class MyStatelessService : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            return new[]
            {
                new ServiceInstanceListener(context =>
                    new HttpSelfHostServer(CreateConfiguration(), CreateRouteHandler())
                )
            };
        }

        private HttpSelfHostConfiguration CreateConfiguration()
        {
            var configuration = new HttpSelfHostConfiguration(new Uri("http://localhost:8080"));
            configuration.Routes.MapHttpRoute("Default", "api/{controller}/{id}", new { id = RouteParameter.Optional });
            return configuration;
        }

        private HttpMessageHandler CreateRouteHandler()
        {
            var configuration = new HttpSelfHostConfiguration(new Uri("http://localhost:8080"));
            var server = new HttpSelfHostServer(configuration);
            return server;
        }

        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            while (true)
            {
                cancellationToken.ThrowIfCancellationRequested();

                // Perform background processing logic here

                await Task.Delay(TimeSpan.FromSeconds(5), cancellationToken);
            }
        }
    }

    public class MyApiController : ApiController
    {
        [HttpGet]
        [Route("api/myendpoint")]
        public IHttpActionResult Get()
        {
            return Ok("Hello from the stateless service API!");
        }
    }
}
```

In this example, we create a stateless service called MyStatelessService that listens on http://localhost:8080 and exposes a single API endpoint at api/myendpoint using an ApiController named MyApiController. The endpoint responds with a simple message when accessed with an HTTP GET request.

Please note that this is a basic example to illustrate the structure of a stateless service API in Azure Service Fabric. In a real-world scenario, you would likely have more complex logic and potentially use dependency injection and other best practices.

Remember to install the necessary NuGet packages, such as Microsoft.AspNet.WebApi.SelfHost, to run this code successfully.

Once you've created the service, you can deploy and run it in Azure Service Fabric, and access the API endpoint using the provided URI (e.g., http://localhost:8080/api/myendpoint).

For a detailed explanation see the youtube video: https://www.youtube.com/watch?v=PZvCBWrqk7w&list=PLalrWAGybpB_dBdtvLXUjOFp78X97lren&index=5
