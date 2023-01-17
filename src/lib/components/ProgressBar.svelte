<script>
    export let value = 0;
    export let max = 100;
    export let color = 'blue';
</script>

<div class="progress">
    <div class="progress-bar" style="width: {value/max * 100}%; background-color: {color}"></div>
</div>

<style>
    .progress {
        display: flex;
        width: 100%;
        height: 20px;
        background-color: #eef4fb;
        border-radius: 15px;
    }

    .progress-bar {
        height: 100%;
        width: 0%;
        border-radius: 15px;
    }
</style>