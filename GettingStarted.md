## Getting started with Simple.OData.Client

Simple.OData.Client is a multiplatform OData client library supporting .NET 4.x, Silverlight, Windows Phone, iOS and Android platforms. The library supports both typed and dynamic syntax (as long as dynamic is supported on the selected platform), doesn't require generation of context or entity classes and fits RESTful nature of OData services. 

The component samples demonstrate the library possibilities on a small mobile app NuGetFinder that can be used to search for NuGet packages, browse results and view package details. The sample app is available for all major mobile platforms (iOS, Android, Windows Phone).

Simple.OData.Client uses non-blocking asynchronous model and depends on Microsoft HttpClient library. HttpClient can be obtained from NuGet (Microsoft.Net.Http package) or referenced as individual DLLs (System.Net.Http, System.Net.Http.Primitives, System.Net.Http.Extensions).

To use Simple.OData.Client in our C# code we need to import its namespace:

<pre>using Simple.OData.Client;</pre>

Now we can create an instance of ODataClient by passing an OData service URL. In a NuGetFinder sample it's a URL of the NuGet OData feed:

<pre>var client = new ODataClient("https://nuget.org/api/v1");
</pre>

After the client is instantiated, we can begin consuming OData service information using either typed or dynamic API. Typed API is supported on all platforms, support for dynamic API is experimental on iOS and available on Windows Phone 8 and Android.

Example of a typed fluent API syntax:

<pre>var packages = client
    .For(&lt;Packages&gt;)
    .Filter(x => x.Title == "Simple.OData.Client")
    .FindEntries();
foreach (var package in packages)
{
    Console.WriteLine(package.Title);
}</pre>

Example of a dynamic fluent API syntax:

<pre>var x = ODataDynamic.Expression;
var packages = client
    .For(x.Packages)
    .Filter(x.Title == "Simple.OData.Client")
    .FindEntries();
foreach (var package in packages)
{
    Console.WriteLine(package.Title);
}</pre>

Both typed and dynamic examples show how simple it is to build and execute an OData request using Simple.OData.Client. Commands can also be built incrementally, as shown in the NuGetFinder sample code that builds an OData request in several steps by applying user-selected options. It begins with specifying OData collection and result count:

<pre>var command = odataClient
    .For&lt;Package&gt;("Packages")
    .Top(count);
</pre>

Next, we add the sort order clause that depends on the user selection:

<pre>
switch (_sortPicker.SelectedIndex)
{
    case 0:
        command.OrderByDescending(x => x.DownloadCount);
        break;
    case 1:
        command.OrderBy(x => x.Id);
        break;
    case 2:
        command.OrderByDescending(x => x.LastUpdated);
        break;
}
</pre>

The code above sets the sort order to be either descending by download count, ascending by package Id or descending by recent update time. In addition a user can specify a search text pattern:

<pre>
if (!string.IsNullOrEmpty(_searchText.Text))
{
    command.Filter(x => x.Title.Contains(_searchText.Text) && x.IsLatestVersion);
}
else
{
    command.Filter(x => x.IsLatestVersion);
}
</pre>

We complete the command generation by restricting the result with the set of fields that our app will need. This will reduce the network traffic.

<pre>
command.Select(x => new
{
    x.Id, 
    x.Title, 
    x.Version, 
    x.LastUpdated, 
    x.DownloadCount, 
    x.VersionDownloadCount, 
    x.PackageSize, 
    x.Authors, 
    x.Dependencies
});
</pre>

Now that the command is ready, we can make a call to fetch the data from the OData service:

<pre>
var results = await command.FindEntriesAsync();
</pre>

Upon the completion of FindEntriesAsync the results are populated with NuGet package information, filtered according to search and field selection criteria and sorted in a given order.

Please refer to the library Wiki pages for more examples and complete documentation. And since Simple.OData.Client is an open-source project, you can always check its source code as well as its hundreds tests.


## Other Resources

* [Wiki pages](https://github.com/object/Simple.OData.Client/wiki)
* [Source Code Repository](https://github.com/object/Simple.OData.Client)
