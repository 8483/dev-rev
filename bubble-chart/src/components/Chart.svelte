<script>
    import { onMount } from "svelte";

    import Bubble from "./Bubble.svelte";
    import Select from "./Select.svelte";

    export let data = [];

    let x, y, bubbleSize;

    // let options = ["salary", "compratio", "headcount"];

    function getValues(type) {
        let points = data.map((item) => {
            return item[type];
        });

        let min = Math.min(...points);
        let max = Math.max(...points);

        let ticks = [min];

        for (let i = min + 5; i < max; i += 5) {
            ticks.push(i);
        }

        let values = {
            points,
            ticks,
            min,
            max,
        };

        console.log(type, values);

        return values;
    }

    let xValues;
    $: xValues = getValues(x, data);

    let yValues;
    $: yValues = getValues(y, data);

    let sizeValues;
    $: sizeValues = getValues(bubbleSize, data);

    function normalize(value, min, max) {
        return ((value - min) / (max - min)) * 100;
    }

    let bubbles = [];
    $: bubbles = data.map((item) => {
        return {
            ...item,
            x: normalize(item[x], xValues.min, xValues.max),
            y: normalize(item[y], yValues.min, yValues.max),
            size: Math.round((item[bubbleSize] / sizeValues.max) * 100),
        };
    });
</script>

<div class="container">
    <div class="selectors">
        <Select bind:value={x} label={"X"} />
        <Select bind:value={y} label={"Y"} />
        <Select bind:value={bubbleSize} label={"Bubble size"} />
    </div>

    <div class="chart">
        <!-- <div class="x-axis">
            {#each xValues.ticks as tick}
                <div class="tick">
                    {tick}
                </div>
            {/each}
        </div> -->

        {#each bubbles as bubble}
            <Bubble {bubble} />
        {/each}
    </div>
</div>

<style>
    .container {
    }

    .selectors {
        display: flex;
        margin-bottom: 30px;
    }

    .chart {
        position: relative;
        width: 500px;
        height: 200px;
    }

    .x-axis {
        display: flex;
    }

    .tick {
        border-top: 1px solid black;
        padding-right: 10px;
    }
</style>
