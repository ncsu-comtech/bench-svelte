<script>
    export let total, filteredTotal, determinedTotal, limit, offset, currentPage, numPages;

    export let classSummary = "bench-summary";

    let shownStart = 0;
    let shownEnd = 0;

    // initial setup of values
    const setup = () => {
        // set summary values
        shownStart = offset;
        if (offset + limit > determinedTotal) {
            shownEnd = determinedTotal;
        } else {
            shownEnd = offset + limit;
        }
    }

    // react to changes
    $: total, filteredTotal, determinedTotal, limit, offset, currentPage, numPages, setup();
</script>

<bench-summary>
    <div role="status" aria-live="polite" class={classSummary} title="Page {currentPage} of {numPages}">
        Showing <b>{shownStart}</b> to <b>{shownEnd}</b> of <b>{determinedTotal}</b> entries {#if determinedTotal < total}(filtered from {total} total entries){/if}
    </div>
</bench-summary>

<style>

    bench-summary {
        grid-area: bench-summary;
    }

    .bench-summary {
        padding: 1rem;
    }
</style>