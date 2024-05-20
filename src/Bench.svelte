<script>
    import BenchPager from "./BenchPager.svelte";
    import BenchSearch from "./BenchSearch.svelte";
    import BenchTable from "./BenchTable.svelte";

    export let data = [];
    export let columns = [];
    export let limit, offset, order, dir, search = undefined;

    export let classBenchContainer = "bench-container";
    export let classTableWrapper = undefined;
    export let classTable = undefined;
    export let classTableBody = undefined;
    export let classTableHead = undefined;
    export let classTableData = undefined;
    export let classTableHeader = undefined;
    export let classTableRow = undefined;
    export let classSearch = undefined;
    export let classPager = undefined;
    export let classSummary = undefined;
    export let classPagerCurrent = undefined;
    export let classPagerSpread = undefined;

    // reset offset on search
    const resetOffset = () => {
        offset = 0;
    }
    $: search, resetOffset();

</script>

<div class={classBenchContainer}>

    <BenchSearch
        bind:searchTerm={search}
        classSearch={classSearch}
    />

    <BenchTable
        data={data}
        columns={columns}
        bind:order={order}
        bind:dir={dir}
        classTableWrapper={classTableWrapper}
        classTable={classTable}
        classTableBody={classTableBody}
        classTableHead={classTableHead}
        classTableData={classTableData}
        classTableHeader={classTableHeader}
        classTableRow={classTableRow}
    />

    <BenchPager
        bind:limit={limit}
        bind:offset={offset}
        total={data.recordsTotal}
        filteredTotal={data.recordsFiltered}
        classPager={classPager}
        classSummary={classSummary}
        classPagerCurrent={classPagerCurrent}
        classPagerSpread={classPagerSpread}
    />

</div>

<style>

    .bench-container {
        display: grid;
        width: 100%;
        grid-template-areas:
            "bench-search bench-search"
            "bench-table  bench-table"
            "bench-pager-summary  bench-pager";
        grid-template-rows: 3em 1fr 3em;
        grid-template-columns: 50% 50%;
    }

</style>
