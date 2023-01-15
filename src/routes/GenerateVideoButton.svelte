<script lang="ts">
	import Dropzone from './Dropzone.svelte';
	import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';
	
	export let imageFile: File;
	export let audioFile: File;

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

<span>
	<button on:click={merge_image_and_audio} disabled={merge_image_and_audio_loading}>
		{#if merge_image_and_audio_loading}
			Loading...
		{:else}
			Click here to generate the video
		{/if}
</span>

<style>

	button {
		color: #364fc7;
		padding: 1.2rem;
		border: 1px solid #364fc7;
		border-radius: 15px;

		background-color: #eef4fb;
		/* width: 30vw; */
		font-family: 'Fira Mono';
		font-size: 1rem;
	}
</style>

