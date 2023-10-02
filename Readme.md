# TYPO3 Extension `numbered_pagination`

Since TYPO3 10 a new pagination API is shipped which supersedes the pagination widget controller which is removed in version 11.0.
[Official documentation](https://docs.typo3.org/m/typo3/reference-coreapi/main/en-us/ApiOverview/Pagination/Index.html)

This extension provides an improved pagination which can be used to paginate array items or query results from Extbase.
The main advantage is that it reduces the amount of pages shown.

**Example**: Imagine 1000 records and 20 items per page which would lead to 50 links.
Using the `NumberedPagination`, you will get something like `< 1 2 ... 21 22 23 24 ... 100 >`

## Installation

Install the extension with `composer require georgringer/numbered-pagination` or by downloading it
from [extensions.typo3.org](https://extensions.typo3.org/extension/numbered_pagination/) or the Extension Manager.

## Usage

Just replace the usage of `SimplePagination` with `\GeorgRinger\NumberedPagination\NumberedPagination` and you are done.
Set the 2nd argument to the maximum number of links which should be rendered.

```php
$itemsPerPage = 10;
$maximumLinks = 15;
$currentPage = $this->request->hasArgument('currentPage') ? (int)$this->request->getArgument('currentPage') : 1;
$paginator = new \TYPO3\CMS\Extbase\Pagination\QueryResultPaginator($allItems, $currentPage, $itemsPerPage);
$pagination = new \GeorgRinger\NumberedPagination\NumberedPagination($paginator, $maximumLinks);
$this->view->assign('pagination', [
    'paginator' => $paginator,
    'pagination' => $pagination,
]);
```

### Templating

```html
<f:for each="{pagination.paginator.paginatedItems}" as="item" iteration="iterator">
    <f:render partial="Item" arguments="{item:item}" />
</f:for>
<f:render partial="Pagination" arguments="{pagination: pagination.pagination, paginator: pagination.paginator, actionName: 'listXYZ'}" />
```

Copy the pagination partial `EXT:numbered_pagination/Resources/Private/Partials/Pagination.html` to your extension or use it directly by providing the path mapping:

```typo3_typoscript
# Example for extension "fo"
plugin.tx_fo.view.partialRootPaths.4483 = EXT:numbered_pagination/Resources/Private/Partials/
```

The default paginator looks like this:

* [1] 2 3 … next
* 1 [2] 3 … next
* prev … 2 [3] 4 … next
* prev … 3 [4] 5
* prev … 3 4 [5]

Here's three how-to's on how to achieve different, still common paginators:

#### Scenario 1

By uncommenting `li.first` and `li.last` and commenting out `li.prev` and `li.next` it looks like this:

* [1] 2 3 … last
* 1 [2] 3 … last
* first 2 [3] 4 last
* first … 3 [4] 5
* first … 3 4 [5]

#### Scenario 1 (alternative)

By **additionally** changing their text to `1` and `{pagination.lastPageNumber}` it looks like this:

* [1] 2 3 … 5
* 1 [2] 3 … 5
* 1 2 [3] 4 5
* 1 … 3 [4] 5
* 1 … 3 4 [5]

#### Scenario 3

By uncommenting `li.first` and `li.last` (and renaming them to `|<` and `>|`) and flipping their position with `li.prev` and `li.next` (and renaming them to `<` and `>`) it looks like this:

* [1] 2 3 … > >|
* 1 [2] 3 … > >|
* |< < … 2 [3] 4 … > >|
* |< < … 3 [4] 5
* |< < … 3 4 [5]
