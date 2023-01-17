<script lang="ts">
  	import GenerateVideoButton from '$lib/components/GenerateVideoButton.svelte';
	import Dropzone from '$lib/components/Dropzone.svelte';
    import ProgressBar from '$lib/components/ProgressBar.svelte';
	
	let imageFile: File;
	let audioFile: File;
	let isLoading = false;
	let progressRatio = 0;

	$: console.log("image:", imageFile?.name, "audio:", audioFile?.name)
</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
		<Dropzone bind:file={imageFile} inputId={"image"} text="an image" />
		<span>+</span>
		<Dropzone bind:file={audioFile} inputId={"audio"} text="an audio"/>
		<span>=</span>
		
		{#if imageFile}
		<GenerateVideoButton
			{imageFile}
			{audioFile}
			bind:isLoading={isLoading}
			bind:progressRatio={progressRatio}/>
		{/if}
</section>
<section id="progress">
	{#if isLoading}
	<ProgressBar value={progressRatio*100} max={100} color="#364fc7" />
	{/if}
</section>
<style>
	section {
		display: flex;
		justify-content: center;
		align-items: center;
		gap: 1rem;
		color: #364fc7;
	}

	#progress {
		width: 100%;
	}
</style>
