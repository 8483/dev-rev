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

    ///////////////////////////

    let canvas = {
        width: 1000,
        height: 1000,
    };
    let canvasElement;

    onMount(() => {
        console.log("canvas", canvasElement);
        let ctx = canvasElement.getContext("2d");

        var grid_size = 25;
        var x_axis_distance_grid_lines = 5;
        var y_axis_distance_grid_lines = 5;
        var x_axis_starting_point = { number: 1, suffix: "\u03a0" };
        var y_axis_starting_point = { number: 1, suffix: "" };

        // canvas width
        var canvas_width = canvas.width;

        // canvas height
        var canvas_height = canvas.height;

        // no of vertical grid lines
        var num_lines_x = Math.floor(canvas_height / grid_size);

        // no of horizontal grid lines
        var num_lines_y = Math.floor(canvas_width / grid_size);

        // Draw grid lines along X-axis
        for (var i = 0; i <= num_lines_x; i++) {
            ctx.beginPath();
            ctx.lineWidth = 1;

            // If line represents X-axis draw in different color
            if (i == x_axis_distance_grid_lines) ctx.strokeStyle = "#000000";
            else ctx.strokeStyle = "#e9e9e9";

            if (i == num_lines_x) {
                ctx.moveTo(0, grid_size * i);
                ctx.lineTo(canvas_width, grid_size * i);
            } else {
                ctx.moveTo(0, grid_size * i + 0.5);
                ctx.lineTo(canvas_width, grid_size * i + 0.5);
            }
            ctx.stroke();
        }

        // Draw grid lines along Y-axis
        for (i = 0; i <= num_lines_y; i++) {
            ctx.beginPath();
            ctx.lineWidth = 1;

            // If line represents Y-axis draw in different color
            if (i == y_axis_distance_grid_lines) ctx.strokeStyle = "#000000";
            else ctx.strokeStyle = "#e9e9e9";

            if (i == num_lines_y) {
                ctx.moveTo(grid_size * i, 0);
                ctx.lineTo(grid_size * i, canvas_height);
            } else {
                ctx.moveTo(grid_size * i + 0.5, 0);
                ctx.lineTo(grid_size * i + 0.5, canvas_height);
            }
            ctx.stroke();
        }

        // Ticks marks along the positive X-axis
        for (i = 1; i < num_lines_y - y_axis_distance_grid_lines; i++) {
            ctx.beginPath();
            ctx.lineWidth = 1;
            ctx.strokeStyle = "#000000";

            // Draw a tick mark 6px long (-3 to 3)
            ctx.moveTo(grid_size * i + 0.5, -3);
            ctx.lineTo(grid_size * i + 0.5, 3);
            ctx.stroke();

            // Text value at that point
            ctx.font = "9px Arial";
            ctx.textAlign = "start";
            ctx.fillText(x_axis_starting_point.number * i + x_axis_starting_point.suffix, grid_size * i - 2, 15);
        }

        // Ticks marks along the negative X-axis
        for (i = 1; i < y_axis_distance_grid_lines; i++) {
            ctx.beginPath();
            ctx.lineWidth = 1;
            ctx.strokeStyle = "#000000";

            // Draw a tick mark 6px long (-3 to 3)
            ctx.moveTo(-grid_size * i + 0.5, -3);
            ctx.lineTo(-grid_size * i + 0.5, 3);
            ctx.stroke();

            // Text value at that point
            ctx.font = "9px Arial";
            ctx.textAlign = "end";
            ctx.fillText(-x_axis_starting_point.number * i + x_axis_starting_point.suffix, -grid_size * i + 3, 15);
        }
    });
</script>

<div class="container">
    <div class="selectors">
        <Select bind:value={x} label={"X"} />
        <Select bind:value={y} label={"Y"} />
        <Select bind:value={bubbleSize} label={"Bubble size"} />
    </div>

    <canvas bind:this={canvasElement} width={canvas.width} height={canvas.height} />

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
