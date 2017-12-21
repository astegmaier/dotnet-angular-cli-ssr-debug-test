# Purpose

This repository is a simplified repro to test server-side debugging capibilities using the latest [ASP.NET Core Angular-Cli-based templates](https://docs.microsoft.com/en-us/aspnet/core/spa/index#installation). Currently, there appears to be an issue with .map files not being generated for the server-side bundles, and this results in a server-side debug experience where you can only debug the minified javascript code, rather than the source typescript files.

# Repro Steps

Prerequisites: npm and the dotnet core cli

1. Clone this repo locally, or follow the "How this repo was built" instructions below to start from scratch.
2. From the `ClientApp` directory, run `npm install`.
3. From the root project directory run `dotnet run`.
4. Navigate a browser window to http://localhost:5000
5. Look for ouput in the command line that looks like this:
```
warn: Microsoft.AspNetCore.NodeServices[0]
      Debugger listening on ws://127.0.0.1:9229/652cb200-9bc5-4de6-bbe8-dbcc3ff3be00
```
6. Open a chrome tab and enter:
```
chrome-devtools://devtools/bundled/inspector.html?experiments=true&v8only=true&ws=127.0.0.1:9229/652cb200-9bc5-4de6-bbe8-dbcc3ff3be00
```
(replace the GUID in this string with the guid seen from the command line)

# Result
In the chrome debugger, you will only see the minified .js files, rather than the full (more useful) source files

# How this repo was built
1. Install the latest Angular-Cli-based templates:
```
dotnet new --install Microsoft.DotNet.Web.Spa.ProjectTemplates::2.0.0-preview1-final
```
2. Create a new directory, navigate to it, and run:
```
dotnet new angular
```
3. Modify the project according to the [instructions in the template documentation under "Server-Side Rendering"](https://docs.microsoft.com/en-us/aspnet/core/spa/angular?tabs=visual-studio#server-side-rendering).
4. Enable server-side debugging by adding these lines to ConfigureServices method in Startup.cs (from [instructions here](https://github.com/aspnet/JavaScriptServices/tree/dev/src/Microsoft.AspNetCore.SpaServices#debugging-your-javascripttypescript-code-when-it-runs-on-the-server)):

```
services.AddNodeServices(options => {
    options.LaunchWithDebugging = true;
    options.DebuggingPort = 9229;
});
```