<script lang="ts">
  	import GenerateVideoButton from './GenerateVideoButton.svelte';
	import Dropzone from './Dropzone.svelte';
	
	let imageFile: File;
	let audioFile: File;

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
		<GenerateVideoButton {imageFile} {audioFile}/>
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

</style>
