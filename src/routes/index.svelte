<script>
    function gotopage(page) {
        location.replace(page);
    }

    const gotoview = () => gotopage("/view");
    const gotocreate = () => gotopage("/create");
</script>

<h1>.poo</h1>
<button on:click={gotoview}>View</button>
<button on:click={gotocreate}>Create</button>