# Bench-Svelte

A quick and dirty DataTables ajax alternative without jQuery using Svelte.

This project will only support dynamic data for pages and supports:
- Sorting columns
- Pagination
- Searching

## Install

```sh
npm install @haught/bench-svelte
```

# Usage

Bench-Svelte requires dynamic data from your backend, this allows efficient displaying of large data sets.

## Getting data
To use Bench-Svelte we need to grab data remotely and it is up to you how uou get such data. Here is an example where you use your own getTable function to go out and get the data.

```javascript
    // define a few variables / defaults
    let url = 'https://example.com/api/v1/'
    let tableData = [];
    let tableOffset = 0;
    let tableLimit = 30;
    let tableSearch = "";
    let tableOrder = 'created_at';
    let tableDir = 'desc';

    // getData will run your getTable function to grab the data
    const getData = async () => {
        tableData = await getTable({
            url: url,
            limit: tableLimit,
            offset: tableOffset,
            order: tableOrder,
            dir: tableDir,
            search: tableSearch});
    };

    // this svelte label will getData if any of these vars change
    $: tableLimit, tableOffset, tableOrder, tableDir, tableSearch, getData();

```

Below is an example of a getTable function which builds a query string in the format `?limit=30&offest=0&order=created_at&dir=desc&search=` for a backend similar to what is used for DataTables ajax.

```javascript
const getTable = async function({url, limit, offset, order, dir, search}) {
    try {
        const paramString = buildQueryParams({
            limit: limit ?? "",
            offset: offset ?? "",
            order: order ?? "",
            dir: dir ?? "",
            search: search ?? "",
        });
        if (paramString) {
            url = url + "?" + paramString;
        }
        const response = await axios.get(url);
        return response.data;
    } catch (error) {
        console.error(error);
        return [];
    }
}
```
The data returned for this example would be in the format:
```json
{
    "recordsTotal": 100,
    "recordsFiltered": 100,
    "results": [
        {"id":1,"name":"Bob","title":"Analyst","status":"At Home"},
        {"id":2,"name":"Olivia","title":"Manager","status":"On Site"},
        {"id":3,"name":"Sandra","title":"CTO","status":"On Site"}
    ]
}
```

## Configure
To configure the Benc-Svelte table, we need to define what columns are to be used from the remote data and various attributes. Here is an example:

```javascript
    let tableColumns = [
        { id: 'id', name: 'ID', hidden: true },
        { id: 'created_at', name: 'Created at', formatter: cell => { return moment(cell).format('MMM Do, H:mm') }},
        { id: 'name', name: 'Name', onClick: e => { nameAction(e)}}},
        { id: 'title', name: 'Title', html: true, onClick: e => { alert(e.target.innerText)} },
        { id: 'status', name: 'Status', sort: false }
    ];
```

The key **id** needs to match the column key from the data (required). <br>
The key **name** will be what is displayed in the table header for the column (required). <br>
The key **hidden** is an optional boolean to hide a specific column from display using css (default: false). <br>
The key **sort** is an optional boolean to enable/disable sorting (default: true). <br>
The key **html** is an optional boolean to allow html in the output (default: false). <br>
The key **formatter** is an optional function to pass the cell data through for formatting. <br>
The key **onClick** is an optional event that can be added to a cell. <br>

## Display
To include the table, simply include the Bench component
```javascript
<Bench
    data={tableData}
    columns={tableColumns}
    bind:order={tableOrder}
    bind:dir={tableDir}
    bind:offset={tableOffset}
    bind:limit={tableLimit}
    bind:search={tableSearch}
/>
```

The **data** prop is a required variable of the entire dataset.<br>
The **columns** prop is a required variable of the column setup.<br>
The **order** prop is a string value of *asc* or *desc*.<br>
The **offset** prop is where in the overall dataset the data will start.<br>
The **limit** prop is how many records will be returned.<br>
The **search** prop is a search string variable.<br>

## Export
The export CSV button will appear when the prop **onExport** is set to reference an async function that will return the same data formated for the table, but without using offset or limit so all of the data is exported.

```javascript
<Bench
    data={tableData}
    columns={tableColumns}
    bind:order={tableOrder}
    bind:dir={tableDir}
    bind:offset={tableOffset}
    bind:limit={tableLimit}
    bind:search={tableSearch}
    onExport={(type) => myExportFn(type)}
/>
```
The export function has one argument of the type of export, for example "csv", that can be used to have different data sent back depending on the type.

# Styling

The default base css class used is **.bench-container** and can be modified using svelte global css, for example:
```css
<style>
    :global(.bench-container search) {
        font-size: 0.8em;
    }
</style>
```

The **.bench-container** class is using css grid for layout and is made up of four areas: **bench-search**, **bench-table**,  **bench-pager-summary**, and **bench-pager** that can be use to adjust the layout somewhat.

You can also use your own classes by changing the **classBenchContainer** property for Bench. For example, if you had your own table styling using *mybench-container* :
```javascript
<Bench
    ...
    classBenchContainer="mybench-container"
/>
```
