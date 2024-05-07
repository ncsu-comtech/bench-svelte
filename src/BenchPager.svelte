<script>
    import BenchSummary from "./BenchSummary.svelte";

    export let total, filteredTotal, limit, offset;

    export let classSummary;
    export let classPager = "bench-pager";
    export let classPagerCurrent = "bench-pager-current";
    export let classPagerSpread = "bench-pager-spread";

    let numPages;
    let currentPage = 1;
    let determinedTotal;
    let shownStart = 0;
    let shownEnd = 0;

    // initial setup of values
    const setup = () => {
        // determine total to use
        if (filteredTotal < total) {
            determinedTotal = filteredTotal;
        } else {
            determinedTotal = total;
        }
        // set number of pages
        numPages = Math.ceil(determinedTotal / limit);
        // determine current page
        if (offset === 0) {
            currentPage = offset + 1;
        } else {
            currentPage = Math.ceil(offset / limit) + 1;
        }
        // determin summary values
        shownStart = offset;
        if (offset + limit > determinedTotal) {
            shownEnd = determinedTotal;
        } else {
            shownEnd = offset + limit;
        }
    }

    // change page
    const setPage = (page) => {
        if (page > 0 && page <= numPages) {
            offset = (page - 1) * limit;
        }
    }

    // react to changes
    $: total, filteredTotal, limit, offset, setup();
</script>

<BenchSummary
    total={total}
    filteredTotal={filteredTotal}
    determinedTotal={determinedTotal}
    limit={limit}
    offset={offset}
    currentPage={currentPage}
    numPages={numPages}
    classSummary={classSummary}
/>

<pager>
    <div class={classPager}>
        <button tabindex="0" title="Previous" aria-label="Previous" class="" on:click={() => setPage(currentPage-1)}>Previous</button><!--

        -->{#if numPages > 1 && currentPage !== 1}<!--
        --><button tabindex="0" title="pagination.firstPage" aria-label="pagination.firstPage" on:click={() => setPage(1)}>1</button><!--
        -->{/if}<!--

        -->{#if currentPage-3 > 1}<!--
        --><button tabindex="-1" class="spread">...</button><!--
        -->{/if}<!--

        -->{#if currentPage-2 > 1 && currentPage+2 >= numPages}<!--
        --><button tabindex="0" class="" title="Page {currentPage-2}" aria-label="Page {currentPage-2}" on:click={() => setPage(currentPage-2)}>{currentPage-2}</button><!--
        -->{/if}<!--

        -->{#if currentPage-1 > 1}<!--
        --><button tabindex="0" class="" title="Page {currentPage-1}" aria-label="Page {currentPage-1}" on:click={() => setPage(currentPage-1)}>{currentPage-1}</button><!--
        -->{/if}<!--

        --><button tabindex="0" class={classPagerCurrent} title="Page {currentPage}" aria-label="Page {currentPage}">{currentPage}</button><!--

        -->{#if currentPage+1 < numPages}<!--
        --><button tabindex="0" class="" title="Page {currentPage+1}" aria-label="Page {currentPage+1}" on:click={() => setPage(currentPage+1)}>{currentPage+1}</button><!--
        -->{/if}<!--

        -->{#if currentPage+2 < numPages && currentPage-2 <= 1}<!--
        --><button tabindex="0" class="" title="Page {currentPage+2}" aria-label="Page {currentPage+2}" on:click={() => setPage(currentPage+2)}>{currentPage+2}</button><!--
        -->{/if}<!--

        -->{#if currentPage+3 < numPages}<!--
        --><button tabindex="-1" class={classPagerSpread}>...</button><!--
        -->{/if}<!--

        -->{#if numPages > currentPage}<!--
        --><button tabindex="0"  title="Page {numPages}" aria-label="Page {numPages}" on:click={() => setPage(numPages)}>{numPages}</button><!--
        -->{/if}<!--

        --><button tabindex="0"  title="Next" aria-label="Next" class="" on:click={() => setPage(currentPage+1)}>Next</button>
    </div>
</pager>

<style>
    pager {
        grid-area: pager;
    }

    .bench-pager {
        float: right;
        padding: 1em;
    }

    .bench-pager button:first-child {
        border-bottom-left-radius: 6px;
        border-left: 1px solid #d2d6dc;
        border-top-left-radius: 6px;
    }

    .bench-pager button:last-child {
        border-bottom-right-radius: 6px;
        border-right: 1px solid #d2d6dc;
        border-top-right-radius: 6px;
    }

    .bench-pager button.bench-pager-current {
        background-color: #f7f7f7;
        font-weight: 700;
    }

    .bench-pager button {
        background-color: #fff;
        border: 1px solid #d2d6dc;
        border-right: none;
        cursor: pointer;
        outline: none;
        padding: 5px 14px;
        -webkit-user-select: none;
        -moz-user-select: none;
        user-select: none;
    }

    .bench-pager button.bench-pager-spread {
        cursor: unset;
    }
</style>
