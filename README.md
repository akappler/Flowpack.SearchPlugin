# Flowpack.SearchPlugin

This plugin is a Search Plugin, to be used together with
[Flowpack.ElasticSearch.ContentRepositoryAdaptor](https://github.com/Flowpack/Flowpack.ElasticSearch.ContentRepositoryAdaptor).

## Installation

Inclusion of the routes from this package into your main `Configuration/Routes.yaml` is no longer needed as of Flow 4.0.

## Configuration

The default configuration uses the default Elasticsearch indexName `typo3cr`. 
If you change the indexName you have also to adapt the settings of your analyzers index name, 
to make autocomplete work again.

### Pagination 

The pagination search results can be configured via Fusion. The following shows the defaults:

    prototype(Flowpack.SearchPlugin:Search).configuration {
        itemsPerPage = 25
        insertAbove = false
        insertBelow = true
        maximumNumberOfLinks = 10
    }

### Custom result rendering

The result list is rendered using a Fusion object of type `nodeType + 'SearchResult'` for each hit.
Thus you can easily adjust the rendering per type like this for an imaginary `Acme.AcmeCom:Product` nodetype:

    prototype(Acme.AcmeCom:ProductSearchResult) < prototype(Neos.Neos:DocumentSearchResult) {
        templatePath = 'resource://Acme.AcmeCom/Private/Templates/SearchResult/ProductSearchResult.html'
    }

Feel free to use the `DocumentSearchResult.html` in the Flowpack.SearchPlugin as an example.


## Search suggestions and autocomplete suggestions

The default search form template comes with a `data-autocomplete-source` attribute pointing to the 
`SuggestController` of this package. Fed with a `node.identifier`, `dimensionCombination` and the `term` parameter 
via a `GET` request, it returns a JSON-encoded object with an array of related term suggestions and autocomplete suggestions 
from Elasticsearch. These are fetched with a term suggester from the `_all` field, i.e. "the fulltext index".

These can be used to provide autocompletion and term suggestion on the search input using a JS library of your choice.

In case you need to build the URI to the suggest controller yourself, this is what the form uses:

    {f:uri.action(action: 'index', controller: 'Suggest', package: 'Flowpack.SearchPlugin', format: 'json', absolute: 1, arguments: {contextNodeIdentifier: node.identifier, dimensionCombination: dimensionCombination})}


## AJAX search

The plugin comes with a controller that can be reached like this per default, using `GET`:

    {f:uri.action(action: 'index', controller: 'AjaxSearch', package: 'Flowpack.SearchPlugin', absolute: 1, arguments: {node: node})}

It expects the search term as a parameter named `q` (as defined in `AjaxSearch.fusion`). This controller
renders the search results and returns them as HTML without any of the page template. It can therefore
be used to request search results via AJAX and display the result by adding it to the DOM as needed.
