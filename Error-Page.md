# Steps for Adding Custom Error-Page

## Step 1: Add the Custom View File under the shared folder and define your view file

## Step 2: Config this HTTP pipeline in Program.cs file

```csharp
    // Configure the HTTP request pipeline.
    if (!app.Environment.IsDevelopment())
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseStatusCodePagesWithReExecute("/{0}"); // Add this line your file
    }
```

- This is used to handle status code pages by re-executing a specific route whenever a certain HTTP status code (such as 404, 500, etc.) is returned.

## Step 3: Make Error Specific Controller file

- In this controller you can match the specific route for the specific route.

```csharp
    using Microsoft.AspNetCore.Mvc;
    namespace CoffeeManagementSystem.Controllers
    {
        public class ErrorController : Controller
        {
            [Route("/{statuscode}")]
            public IActionResult HandleError(int statuscode)
            {
                if (statuscode == 404)
                {
                    return View("Error");
                }
                return View("Error404");
            }
        }
    }
```

- Define the `[Route("/{statuscode}")]` specific route match.
- You can match all the specific error code route match.

## Step 4: Run Your Application

- And test match the non-existing route matching
- `http://localhost:44705/Non-existing route`
