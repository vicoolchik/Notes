+++
title = 'Global Error Handling'
date = 2024-09-30T19:58:16+03:00
draft = false
tags = ['Clean Architecture']
+++

# Global Error Handling in ASP.NET Core

## Introduction

Error handling is essential in web application development to ensure that unhandled exceptions are appropriately managed and that clients receive meaningful error responses. In ASP.NET Core, global error handling can be achieved using several techniques, each with its advantages and suitable use cases. This article will guide you through primary approaches:

1. **Error Handling Middleware**
2. **Exception Filter Attribute**
3. **UseExceptionHandler Middleware with Custom Error Endpoint**

We will also delve into using the `ProblemDetails` response format to provide standardized error information to clients, as recommended by RFC 7807.

## Approach 1: Error Handling Middleware

### Implementation

The first approach involves creating custom middleware to catch exceptions that occur during the processing of HTTP requests. Here's how you can implement it:

#### Creating the Middleware

```csharp
using System.Net;
using System.Text.Json;

namespace YourApp.Api.Middleware;

public class ErrorHandlingMiddleware
{
    private readonly RequestDelegate _next;

    public ErrorHandlingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            await HandleExceptionAsync(context, ex);
        }
    }

    private static Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        var code = HttpStatusCode.InternalServerError; // 500 if unexpected

        var result = JsonSerializer.Serialize(new { error = "An error occurred while processing your request." });
        context.Response.ContentType = "application/json";
        context.Response.StatusCode = (int)code;

        return context.Response.WriteAsync(result);
    }
}
```

#### Registering the Middleware

In your `Program.cs` or `Startup.cs`, register the middleware:

```csharp
using YourApp.Api.Middleware;

var builder = WebApplication.CreateBuilder(args);
// Configure services
var app = builder.Build();

// Add the error handling middleware
app.UseMiddleware<ErrorHandlingMiddleware>();

// Configure the HTTP request pipeline
app.UseHttpsRedirection();
app.MapControllers();
app.Run();
```

### How It Works

- The middleware wraps the next delegate in the pipeline with a `try-catch` block.
 If an exception occurs during request processing—whether before reaching the controller, during controller execution, or after—it is caught, and a generic error response is sent to the client.
- The middleware sets the response's status code to `500 Internal Server Error` and returns a JSON-formatted error message.
    This provides a unified error response, which is returning consistent error messages to the client.
    **Example Server Response:**
    ```http 
    HTTP/1.1 500 Internal Server Error
    Connection: close
    Content-Type: application/json
    Date: [Response Date]
    Server: [Server Name]
    Transfer-Encoding: chunked

    {
    "error": "An error occurred while processing your request."
    }
    ```

**It catches exceptions in**:
  - In controllers or actions.
  - In services or repositories called by controllers.
  - In middleware registered after the Error Handling Middleware.
  
**Exceptions Not Caught**:
  - In middleware components that are registered before the Error Handling Middleware will not be caught by it.
  - After the response has begun being sent to the client, such as during response streaming, are not caught.

## Approach 2: Exception Filter Attribute

### Implementation

The second approach uses an exception filter attribute that can be applied globally or to specific controllers. 

There are two main ways to implement the exception filter:

1. **Returning a Simple JSON Error Response**
2. **Using ProblemDetails for Detailed Error Information**

#### Returning a Simple JSON Error Response

In this variant, the exception filter catches exceptions and returns a simple JSON response with an error message.

**Creating the Exception Filter:**

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;

namespace YourApp.Api.Filters;

public class ErrorHandlingFilterAttribute : ExceptionFilterAttribute
{
    public override void OnException(ExceptionContext context)
    {
        var exception = context.Exception;

        context.Result = new ObjectResult(new 
        { 
            error = "An error occurred while processing your request." 
        })
        {
            StatusCode = 500
        };

        context.ExceptionHandled = true;
    }
}
```

In this code:

- The `OnException` method is overridden to handle exceptions.
- When an exception occurs, the filter creates a new `ObjectResult` containing a JSON object with an `error` property.
- The status code is set to `500 Internal Server Error`.
- `context.ExceptionHandled` is set to `true` to indicate that the exception has been handled.

**Example Server Response:**

```http 
HTTP/1.1 500 Internal Server Error
Content-Type: application/json;
Date: [Response Date]
Server: [Server Name]
Transfer-Encoding: chunked

{
  "error": "An error occurred while processing your request."
}
```

This response provides a simple error message in JSON format, which can be easily parsed by clients.

#### Using ProblemDetails for Detailed Error Information

In this variant, the exception filter uses the `ProblemDetails` class to return standardized error information according to [RFC 7807](https://tools.ietf.org/html/rfc7807).

**Creating the Exception Filter:**

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;

namespace YourApp.Api.Filters;

public class ErrorHandlingFilterAttribute : ExceptionFilterAttribute
{
    public override void OnException(ExceptionContext context)
    {
        var exception = context.Exception;

        var problemDetails = new ProblemDetails
        {
            Type = "https://tools.ietf.org/html/rfc7231#section-6.6.1",
            Title = "An error occurred while processing your request.",
            Status = (int)HttpStatusCode.InternalServerError,
        };

        context.Result = new ObjectResult(problemDetails);
        context.ExceptionHandled = true;
    }
}
```

In this code:

- A `ProblemDetails` object is created to represent the error.
- The `Type` property provides a URI reference to the problem type, according to the HTTP specification.
- The `Title` and `Status` properties provide a human-readable summary and the HTTP status code, respectively.
- The `ProblemDetails` object is set as the result.
- `context.ExceptionHandled` is set to `true` to prevent the exception from propagating further.

**Example Server Response:**

```http
HTTP/1.1 500 Internal Server Error
Content-Type: application/problem+json;
Date: [Response Date]
Server: [Server Name]
Transfer-Encoding: chunked

{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.6.1",
  "title": "An error occurred while processing your request.",
  "status": 500
}
```

This response conforms to the Problem Details specification, providing standardized error information that clients can utilize for better error handling.

#### Applying the Filter Globally

To apply the exception filter globally, register it in your `Program.cs` or `Startup.cs` file:

```csharp
using YourApp.Api.Filters;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers(options =>
{
    options.Filters.Add<ErrorHandlingFilterAttribute>();
});

var app = builder.Build();

app.UseHttpsRedirection();
app.MapControllers();
app.Run();
```
This ensures the exception filter is applied to all controllers in the application.

#### Applying the Filter to a Specific Controller
You can also apply the filter to a single controller using an attribute:

```csharp
[ApiController]
[Route("auth")]
[ErrorHandlingFilter] // Exception handling filter
public class AuthenticationController : ControllerBase
{
    // Controller actions
}
```

This applies the filter only to the `AuthenticationController`, making it useful when you need custom exception handling for specific controllers.

### How It Works

- The `ErrorHandlingFilterAttribute` inherits from `ExceptionFilterAttribute` and overrides the `OnException` method. When an unhandled exception occurs within a controller action, the filter intercepts it and provides a standardized error response.
-  The flag `context.ExceptionHandled = true` ensures that the exception is marked as handled, preventing it from propagating further in the request pipeline.

**It catches exceptions in**:
  - Controller actions.
  - Services or repositories invoked by controllers, as long as the exception is propagated back to the controller action.
  
**Exceptions Not Caught**:
  - Exceptions that occur **outside of MVC controllers** (e.g., in middleware or during model binding) are not caught.

This approach allows centralized error handling for controller actions, ensuring clean, consistent error responses without cluttering the controllers with exception-handling logic.

## Approach 3: UseExceptionHandler Middleware and Error Endpoint

### Implementation

The third approach leverages the built-in `UseExceptionHandler` middleware, which redirects unhandled exceptions to a specified endpoint.

#### Configuring the Middleware

In `Program.cs` or `Startup.cs`, configure the middleware to catch unhandled exceptions and route them to `/error`:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Catches unhandled exceptions and redirects to the "/error" endpoint
app.UseExceptionHandler("/error");

app.UseHttpsRedirection();
app.MapControllers();
app.Run();
```

#### Creating the Error Controller

Create an `ErrorsController` to handle the errors redirected by the middleware:

```csharp
using Microsoft.AspNetCore.Diagnostics;
using Microsoft.AspNetCore.Mvc;

namespace YourApp.Api.Controllers;

[ApiController]
[Route("/error")]
public class ErrorsController : ControllerBase
{
    public IActionResult HandleError()
    {
        Exception? exception = HttpContext.Features.Get<IExceptionHandlerFeature>()?.Error;
        return Problem(); // Returns a default ProblemDetails response (500 status)
    }
}
```

**Example Response (500 Internal Server Error)**:

```http
HTTP/1.1 500 Internal Server Error
Content-Type: application/problem+json; charset=utf-8
Date: [Response Date]
Server: [Server Name]
Cache-Control: no-cache, no-store
Expires: -1
Pragma: no-cache
Transfer-Encoding: chunked

{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.6.1",
  "title": "An error occurred while processing your request.",
  "status": 500,
  "traceId": "[Trace ID]"
}
```

**Customizing the Response**

You can customize the error response with a custom title and status code:

```csharp
return Problem(title: exception?.Message, statusCode: 400);
```

**Example Response (400 Bad Request)**:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
Date: [Response Date]
Server: [Server Name]
Cache-Control: no-cache, no-store
Expires: -1
Pragma: no-cache
Transfer-Encoding: chunked

{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "User with given email already exists.",
  "status": 400,
  "traceId": "[Trace ID]"
}
```

This setup centralizes error handling, allowing you to customize error responses while maintaining a clean and maintainable codebase.

#### UseExceptionHandler Middleware without a Custom Error Endpoint

In this approach, exceptions are handled directly in a middleware extension, without a dedicated controller. 

**Middleware Extension Method**

Here's the example of handling global errors without a custom error endpoint or manual `traceId` addition:

```csharp
using Microsoft.AspNetCore.Diagnostics;

public static class WebApplicationExtensions
{
    public static WebApplication UseGlobalErrorHandling(this WebApplication app)
    {
        app.UseExceptionHandler("/error");

        app.Map("/error", (HttpContext httpContext) =>
        {
            Exception? exception = httpContext.Features.Get<IExceptionHandlerFeature>()?.Error;

            return Results.Problem();
        });

        return app;
    }
}
```
**Registering in `Program.cs`**

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.UseGlobalErrorHandling();
app.UseHttpsRedirection();
app.MapControllers();
app.Run();

```

**Example Response (500 Internal Server Error)**:
> The `traceId` is not automatically included.
```http
HTTP/1.1 500 Internal Server Error
Content-Type: application/problem+json; charset=utf-8
Date: [Response Date]
Server: [Server Name]
Cache-Control: no-cache, no-store
Expires: -1
Pragma: no-cache
Transfer-Encoding: chunked

{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.6.1",
  "title": "An error occurred while processing your request.",
  "status": 500
}
```

### How It Works

- The `UseExceptionHandler` middleware catches unhandled exceptions that occur **after the middleware is registered** in the pipeline and redirects the request to a specified endpoint (e.g., `/error`).
- The `ErrorsController` processes the error and returns a standardized response using the `Problem()` method, which constructs a `ProblemDetails` object compliant with RFC 7807.
  
**Exceptions Caught**:
  - **In controllers** during the execution of actions.
  - In **services** or **repositories** called by controllers, provided they propagate up to the controller.
  - In **middleware registered after** the `UseExceptionHandler` middleware.
  - Any other exceptions in the pipeline, as long as they happen **after the `UseExceptionHandler` middleware is registered**.
  
**Exceptions Not Caught**:
  - **Before the `UseExceptionHandler` middleware** is registered in the pipeline.
  - **After the response has started being sent** to the client (e.g., during response streaming).

## Using ProblemDetails for Error Responses

### ProblemDetails Response Format

The `ProblemDetails` class in ASP.NET Core implements the RFC 7807 specification for error responses. It standardizes error responses with properties such as:

- `Type`: A URI reference that identifies the problem type.
- `Title`: A short, human-readable summary of the problem type. This should remain consistent for similar errors (e.g., "Bad Request" or "Unauthorized").
- `Status`: The HTTP status code.
- `Detail`: A human-readable explanation that is specific to this occurrence of the problem. Unlike the Title, the Detail may vary depending on the specific error (e.g., "Invalid input parameters" or "User does not have sufficient credits").
Avoid parsing information from Detail—instead, use extension fields for structured error data that the client might need to handle programmatically.
- `Instance`: A URI reference that identifies the specific occurrence of the problem.

Here's an example of a `ProblemDetails` response:

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "Bad Request",
  "status": 400,
  "detail": "Invalid input parameters.",
  "instance": "/api/values/5",
  "traceId": "|7ff13e2b-4dd3d7fbfb934bce."
}
```
### Extending ProblemDetails for Global Error Handling

Customizing `ProblemDetails` gives developers control to add extra information like `traceId`, modify error messages, or include additional metadata.

**Customizing ProblemDetails**
There are two main ways to customize `ProblemDetails` based on your .NET version:

- **In .NET 7+**, you can use `AddProblemDetails()` to modify error responses.
- **In earlier .NET versions**, you can create a custom `ProblemDetailsFactory` to handle error logic.

#### Customizing with `AddProblemDetails()` in .NET 7+

To enhance error responses in .NET 7+ using `AddProblemDetails()`, we can add extra fields to `ProblemDetails` or modify existing ones.

```csharp
public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddGlobalErrorHandling(this IServiceCollection services)
    {
        services.AddProblemDetails(options =>
        {
            // Customize ProblemDetails to add traceId and any other fields
            options.CustomizeProblemDetails = context =>
            {
                context.ProblemDetails.Extensions["traceId"] = context.HttpContext.TraceIdentifier;
                context.ProblemDetails.Extensions["customField"] = "CustomValue"; // Additional custom field
            };
        });

        return services;
    }
}
```

In this example, the `CustomizeProblemDetails` delegate is used to add custom information to every error response, such as a `traceId`, or any other custom fields that you want to include in your API responses.

**Registering Global Error Handling in `Program.cs`**

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add global error handling with ProblemDetails customization
builder.Services.AddGlobalErrorHandling();

var app = builder.Build();

// Ensure exceptions are caught and redirected to the error endpoint
app.UseExceptionHandler("/error");
app.UseHttpsRedirection();
app.MapControllers();
app.Run();
```

**Example of Custom Response**

Here’s an example of what an error response might look like with the added `traceId` and custom field:

```http
HTTP/1.1 500 Internal Server Error
Content-Type: application/problem+json; charset=utf-8
Date: [Response Date]
Server: [Server Name]
Cache-Control: no-cache, no-store
Expires: -1
Pragma: no-cache
Transfer-Encoding: chunked

{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.6.1",
  "title": "An error occurred while processing your request.",
  "status": 500,
  "traceId": "[Trace ID]",
  "customField": "CustomValue"
}
```

#### Customizing with `ProblemDetailsFactory` in Earlier .NET Versions

In .NET versions earlier than 7, the same functionality can be achieved by implementing a custom `ProblemDetailsFactory`.

```csharp
using System.Diagnostics;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Infrastructure;
using Microsoft.AspNetCore.Mvc.ModelBinding;
using Microsoft.Extensions.Options;

namespace YourApp.Api.Errors;

public class CustomProblemDetailsFactory : ProblemDetailsFactory
{
    private readonly ApiBehaviorOptions _options;

    public CustomProblemDetailsFactory(IOptions<ApiBehaviorOptions> options)
    {
        _options = options?.Value ?? throw new ArgumentNullException(nameof(options));
    }

    public override ProblemDetails CreateProblemDetails(
        HttpContext httpContext,
        int? statusCode = null,
        string? title = null,
        string? type = null,
        string? detail = null,
        string? instance = null)
    {
        statusCode ??= 500;

        var problemDetails = new ProblemDetails
        {
            Status = statusCode,
            Title = title ?? "An error occurred while processing your request.",
            Type = type,
            Detail = detail,
            Instance = instance,
        };

        ApplyProblemDetailsDefaults(httpContext, problemDetails, statusCode.Value);

        return problemDetails;
    }

    public override ValidationProblemDetails CreateValidationProblemDetails(
        HttpContext httpContext,
        ModelStateDictionary modelStateDictionary,
        int? statusCode = null,
        string? title = null,
        string? type = null,
        string? detail = null,
        string? instance = null)
    {
        statusCode ??= 400;

        var problemDetails = new ValidationProblemDetails(modelStateDictionary)
        {
            Status = statusCode,
            Title = title ?? "One or more validation errors occurred.",
            Type = type,
            Detail = detail,
            Instance = instance,
        };

        ApplyProblemDetailsDefaults(httpContext, problemDetails, statusCode.Value);

        return problemDetails;
    }

    private void ApplyProblemDetailsDefaults(HttpContext httpContext, ProblemDetails problemDetails, int statusCode)
    {
        problemDetails.Status ??= statusCode;

        if (_options.ClientErrorMapping.TryGetValue(statusCode, out var clientErrorData))
        {
            problemDetails.Title ??= clientErrorData.Title;
            problemDetails.Type ??= clientErrorData.Link;
        }

        var traceId = Activity.Current?.Id ?? httpContext?.TraceIdentifier;
        if (traceId != null)
        {
            problemDetails.Extensions["traceId"] = traceId;
        }

        // Add custom properties
        problemDetails.Extensions.Add("customProperty", "customValue");
    }
}
```

**Registering the Custom Factory**

In `Program.cs`, register this custom factory as a service:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Register the custom ProblemDetailsFactory
builder.Services.AddSingleton<ProblemDetailsFactory, CustomProblemDetailsFactory>();

var app = builder.Build();
app.UseExceptionHandler("/error");
app.UseHttpsRedirection();
app.MapControllers();
app.Run();
```

**Example of Custom Response with Custom Factory**

Here’s a similar error response with custom data from the `ProblemDetailsFactory`:

```http
HTTP/1.1 500 Internal Server Error
Content-Type: application/problem+json; charset=utf-8
Date: [Response Date]
Server: [Server Name]
Cache-Control: no-cache, no-store
Expires: -1
Pragma: no-cache
Transfer-Encoding: chunked

{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.6.1",
  "title": "An error occurred.",
  "status": 500,
  "traceId": "[Trace ID]",
  "customProperty": "customValue"
}
```

### Wrap-up

By customizing `ProblemDetails`, you can provide detailed and useful error responses in a standardized format, allowing better error handling for clients. With `.NET 7+`, this process is made easier through `AddProblemDetails()`, while earlier versions require a custom `ProblemDetailsFactory` for similar functionality. In both cases, you can ensure that error responses are consistent, informative, and secure.

## Conclusion

Global error handling in ASP.NET Core can be achieved through various methods, each with its own advantages. By using middleware, exception filters, or the built-in `UseExceptionHandler` middleware, you can catch exceptions and provide consistent, meaningful responses to clients. Leveraging the `ProblemDetails` response format helps standardize error responses and improves client-side error handling.

| **Method**                    | **Exceptions in Controllers** | **Exceptions in Services or Repositories** | **Exceptions in Middleware Registered After** | **Exceptions in Middleware Registered Before** | **Exceptions After Response Sent** |
|-------------------------------|-------------------------------|--------------------------------------------|-----------------------------------------------|-----------------------------------|---------------------------------|
| **Error Handling Middleware**  | **Yes**                        | **Yes**                                     | **Yes**                                       | **No**                            | **No**                          |
| **Exception Filter Attribute** | **Yes**                        | **Yes**    | **No**                                        | **No**                            | **No**                          |
| **UseExceptionHandler Middleware** | **Yes**                        | **Yes**                                     | **Yes**                                       | **No**                            | **No**                          |

### Remark
While these approaches centralize exception handling, proper error **flow management** is crucial. Simply allowing exceptions to bubble up will result in a generic 500 response. Services, like a review service, should handle errors correctly to provide meaningful responses instead of unnecessary exceptions.
