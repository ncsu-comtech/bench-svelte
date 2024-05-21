<script>
  import Bench from '../../../src/Bench.svelte';
  import { onMount } from 'svelte';
  import moment from "moment";
  import axios from "axios";

  let tableData, tableLimit=5, tableOffset=0, tableOrder, tableDir, tableSearch;

  const titleAction = function(e) {
    if (e.target.classList.contains('selected')) {
      console.log('Unselect "' + e.target.innerText + '"')
      e.target.classList.remove('selected');
    } else {
      console.log('Select "' + e.target.innerText + '"')
      e.target.classList.add('selected');
    }
  }

  const tableColumns = [
        { id: 'key', name: 'KEY', hidden: true },
        { id: 'author_name', name: 'Author', sort: false, onClick: e => { alert(e.target.innerText)}  },
        { id: 'title', name: 'Title', html: true, onClick: e => { titleAction(e)} },
        { id: 'publish_date', name: "Published", formatter: cell => { return moment(cell).format('MMM Do, H:mm') }, sort: false},
        { id: 'number_of_pages_median', name: 'Pages', sort: false }
    ];

  const getTable = async function({url, limit=5, offset=0, order, dir, search}) {
    try {
      const paramString = buildQueryParams({
        limit: limit ?? "",
        offset: offset ?? "",
        sort: order ?? "",
        // dir: dir ?? "",
        q: search ?? "",
        fields: "key,author_name,title,number_of_pages_median,publish_date",
      });
      if (paramString) {
        url = url + "?" + paramString;
      }
      console.log(url);
      const response = await axios.get(url);
      if (response.headers['content-type'] !== 'application/json') {
        console.error('non-json response from status/view');
        return false;
      } else {
        return formatResult(response.data);
      }
    } catch (error) {
      console.error(error);
      return false;
    }
  };

  const buildQueryParams = function(params) {
    let queryParams = new URLSearchParams();
    Object.entries(params).map(([key, value]) => {
      if (value !== "") {
        return queryParams.set(key, value)
      }
      return queryParams
    });
    return queryParams.toString();
  }

  const formatResult = function(res) {
    let output = {
      "recordsTotal": 128,
      "recordsFiltered": 128,
      "results": []
    }
    for (let i = 0; i < res.docs.length; i++) {
      output.results.push({
        key: res.docs[i]['key'] ?? 22,
        publish_date: res.docs[i]['publish_date'][0] ?? 'Feb 20, 1978',
        author_name: res.docs[i]['author_name'][0] ?? 'Unknown',
        title: res.docs[i]['title'] ?? 'Unknown',
        number_of_pages_median: res.docs[i]['number_of_pages_median'] ?? 100,
      })
    }
    return output;
  }

  const updateTable = async function() {
    tableData = await getTable({
      url: "https://openlibrary.org/search.json",
      limit: tableLimit,
      offset: tableOffset,
      order: tableOrder,
      dir: tableDir,
      search: "subject:beer"+ (tableSearch ? " title:" + tableSearch : '')
    });
  }

  onMount(async () => {
    await updateTable();
  });

  $: tableLimit, tableOffset, tableOrder, tableDir, tableSearch, updateTable();


</script>
<h3>Basic example:</h3>
<Bench
    data={tableData}
    columns={tableColumns}
    bind:order={tableOrder}
    bind:dir={tableDir}
    bind:offset={tableOffset}
    bind:limit={tableLimit}
    bind:search={tableSearch}
/>
<br />
<hr>
<h3>Custom css example:</h3>
<Bench
    data={tableData}
    columns={tableColumns}
    bind:order={tableOrder}
    bind:dir={tableDir}
    bind:offset={tableOffset}
    bind:limit={tableLimit}
    bind:search={tableSearch}
    classBenchContainer="mybench-container"
/>


<style>


:global(td.selected) {
  font-weight: bold;
  background-color: rgba(255, 255, 0, 0.5) !important;
}

</style>
