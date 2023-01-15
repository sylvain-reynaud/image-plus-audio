<script lang="ts">
  	import GenerateVideoButton from './GenerateVideoButton.svelte';
	import Dropzone from './Dropzone.svelte';
	import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';
	
	let imageFile: File;
	let audioFile: File;

	$: console.log("image:", imageFile?.name, "audio:", audioFile?.name)


	let merge_image_and_audio_loading = false;
	async function merge_image_and_audio() {
		merge_image_and_audio_loading = true;
		// ffmpeg -r 1 -loop 1 -i image.jpg -i audio.wav -acodec copy -r 1 -shortest -vf scale=1280:1280 result.flv
		const ffmpeg = createFFmpeg({
			log: true,
		});
		await ffmpeg.load();
		ffmpeg.FS('writeFile', imageFile.name, await fetchFile(imageFile));
		ffmpeg.FS('writeFile', audioFile.name, await fetchFile(audioFile));

		await ffmpeg.run(
			'-r',
			'1',
			'-loop',
			'1',
			'-i',
			imageFile.name,
			'-i',
			audioFile.name,
			'-acodec',
			'copy',
			'-r',
			'1',
			'-shortest',
			'-vf',
			'scale=1280:1280',
			'result.flv'
		);

		const data = ffmpeg.FS('readFile', 'result.flv');

		const url = URL.createObjectURL(
			new Blob([data.buffer], { type: 'video/mp4' })
		);

		const a = document.createElement('a');
		a.href = url;
		a.download = 'result.flv';
		a.click();
		merge_image_and_audio_loading = false;
	}
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
