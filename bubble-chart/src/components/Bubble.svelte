<script>
    export let bubble;
</script>

<div
    class="container"
    style="
        left: {bubble.x}%;
        top: {bubble.y}%;

        width: {bubble.size}px;
        height: {bubble.size}px;

        background: rgba({Math.random() * 255}, {Math.random() * 255}, {Math.random() * 255}, 0.5)
    "
>
    <div class="title">
        {bubble.title}
    </div>
</div>

<style>
    .container {
        position: absolute;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 11px;
    }

    .title {
        /* position: relative; */
    }
</style>
