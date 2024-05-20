<script>

    export let searchTerm;
    export let classSearch = 'bench-search';

</script>

<bench-search>
    <div class="bench-search-container"><input type="search" placeholder="Type a keyword..." aria-label="Type a keyword..." class={classSearch} bind:value={searchTerm} on:input></div>
</bench-search>

<style>

    bench-search {
        grid-area: bench-search;
    }

    .bench-search {
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none;
        background-color: #fff;
        border: 1px solid #d2d6dc;
        border-radius: 5px;
        line-height: 1.2rem;
        outline: none;
        padding: 0.6rem 1rem;
        width: 250px;
    }

</style>
