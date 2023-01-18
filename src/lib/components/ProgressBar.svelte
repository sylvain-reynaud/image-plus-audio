<script lang="ts">
    export let value = 0;
    export let max = 100;
    export let color = 'blue';

    // This code calculates the estimated time remaining to complete a task
// based on the current progress of the task.
// startTime: time when the task was started
// estimatedTime: estimated time remaining to complete the task
// progressRatio: the ratio of the task that has been completed
// newRatio: the updated ratio of the task that has been completed

function msToTime(milliseconds: number) {
  const seconds = 1 + (milliseconds / 1000); // add 1 second to avoid 0:00:00
  const hours = Math.floor(seconds / 3600);
  const minutes = Math.floor((seconds % 3600) / 60);
  const secs = Math.floor(seconds % 60);

  return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
}


let startTime = Date.now();
let estimatedTime: number;

$: estimatedTime = ((Date.now() - startTime) / value) * (100 - value)
$: console.log(value)
</script>

<div class="progress">
    <div class="progress-bar" style="width: {value/max * 100}%; background-color: {color}"></div>
</div>
{#if !Number.isNaN(estimatedTime)}
    <div><p>Finish in {msToTime(estimatedTime)}</p></div>
    {/if}

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