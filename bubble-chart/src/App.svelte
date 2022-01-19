<script>
    import { onMount } from "svelte";

    import Chart from "./components/Chart.svelte";

    let data;

    onMount(async () => {
        const res = await fetch("https://mocki.io/v1/18936d28-2f79-4840-b146-5622e8ad1e77");
        data = await res.json();
    });
</script>

<Chart {data} />
