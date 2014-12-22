## Simple.OData.Client

Simple.OData.Client is a multiplatform OData client library supporting .NET 4.x, Silverlight, Windows Phone, iOS and Android platforms. The library supports both typed and dynamic syntax (as long as dynamic is supported on the selected platform), doesn't require generation of context or entity classes and fits RESTful nature of OData services.

Simple.OData.Client uses non-blocking asynchronous model and internally depends on Microsoft HttpClient library. 

## Xamarin Store component and NuGet package

Simple.OData.Client is packaged as both Xamarin Store component and a NuGet package (available from NuGet.org). Both library sources correspond to the same library. If the application is developed for several platforms, it can be more convenient to use Simple.OData.Client NuGet package that is optimized for binary reuse and targets multiple platforms with the same assembly. If the application targets a single platorm, choice of either Xamarin Store component or NuGet package is just a matter of a developer's preference. In any case it's a NuGet package that is installed, Xamarin Store component is just a wrapper around the NuGet package.

Simple.OData.Client NuGet package supports all OData protocols (versions 1-4), but the unified support comes at the cost of the package footprint. If you know the version of the OData service you are going to use, you can install from nuget.org the version that targets the specific OData protocol (Simple.OData.V3.Client or Simple.OData.V4.Client). In that case you shouldn't be using a Xamarin Store component but install the chosen package directly from nuget.org.

## Quick Usage

Simple.OData.Client comes with typed and dynamic API. Typed API is supported on all platforms, support for dynamic API is experimental on iOS and available on other platforms.

Example of a typed API syntax:

<pre>
var client = new ODataClient("https://nuget.org/api/v1");

var packages = client
    .For&lt;Packages&gt;()
    .Filter(x => x.Title == "Simple.OData.Client")
    .FindEntries();

foreach (var package in packages)
{
    Console.WriteLine(package.Title);
}</pre>

Example of a dynamic API syntax:

<pre>
var client = new ODataClient("https://nuget.org/api/v1");
var x = ODataDynamic.Expression;

var packages = client
    .For(x.Packages)
    .Filter(x.Title == "Simple.OData.Client")
    .FindEntries();

foreach (var package in packages)
{
    Console.WriteLine(package.Title);
}</pre>

See "Getting Started" document for an example of using Simple.OData.Client to build an application NuGetFinder that searches NuGet OData feed.

## Other Resources

* [Wiki pages](https://github.com/object/Simple.OData.Client/wiki)
* [Source Code Repository](https://github.com/object/Simple.OData.Client)


