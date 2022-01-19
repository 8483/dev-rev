<script>
    export let label;
    export let value;
</script>

<div class="container">
    {label}

    <select bind:value>
        <option value="salary">salary</option>
        <option value="compratio">compratio</option>
        <option value="headcount">headcount</option>
    </select>
</div>

<style>
    .container {
        margin-right: 20px;
    }
</style>
