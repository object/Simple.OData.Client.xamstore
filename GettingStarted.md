## Getting started with Simple.OData.Client

Simple.OData.Client component comes with a sample that demonstrates the library possibilities on a small mobile app NuGetFinder that can be used to search NuGet packages, browse results and view package details. The sample app is available for all major mobile platforms (iOS, Android, Windows Phone).

To use Simple.OData.Client in our C# code we need to ensure the project references Microsoft HttpClient assembly (see "Details" document for more). Then we can import Simple.OData.Client namespace:

<pre>using Simple.OData.Client;</pre>

In case you are building an iOS application you will have to ensure it holds a reference to a respective OData adapter (V3 or V4), otherwise iOS linker may not include it in the binaries:

<pre>// Specify V3 or V4 adapter (or both) depending on the protocol version of the OData service
Simple.OData.Client.V3Adapter.Reference(); </pre>

Next we create an instance of ODataClient by passing an OData service URL. In a NuGetFinder sample it's a URL of the NuGet OData feed:

<pre>var client = new ODataClient("https://nuget.org/api/v1");</pre>

After the client is instantiated, we can begin consuming OData service information.
NuGetFinder sample code builds an OData request incrementally by applying user-selected options. It begins with specifying OData collection and result count:

<pre>var command = odataClient
    .For&lt;Package&gt;("Packages")
    .Top(count);
</pre>

Next, the sort order clause is added. The sort depends on the user selection:

<pre>switch (_sortPicker.SelectedIndex)
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

<pre>if (!string.IsNullOrEmpty(_searchText.Text))
{
    command.Filter(x => x.Title.Contains(_searchText.Text) && x.IsLatestVersion);
}
else
{
    command.Filter(x => x.IsLatestVersion);
}
</pre>

We complete the command generation by restricting the result with the set of fields that our app will need. This will reduce the network traffic.

<pre>command.Select(x => new
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

<pre>var results = await command.FindEntriesAsync();
</pre>

Upon the completion of FindEntriesAsync the results are populated with NuGet package information, filtered according to search and field selection criteria and sorted in a given order.

Please refer to the library Wiki pages for more examples and complete documentation. And since Simple.OData.Client is an open-source project, you can always check its source code as well as its hundreds tests.


## Other Resources

* [Wiki pages](https://github.com/object/Simple.OData.Client/wiki)
* [Source Code Repository](https://github.com/object/Simple.OData.Client)
