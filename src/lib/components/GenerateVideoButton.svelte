<script lang="ts">
	const { createFFmpeg, fetchFile } = FFmpeg;
	// import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';
	
	export let imageFile: File;
	export let audioFile: File;
	export let progressRatio = 0;

	export let isLoading = false;
	async function merge_image_and_audio() {
		isLoading = true;
		// ffmpeg -r 1 -loop 1 -i image.jpg -i audio.wav -acodec copy -r 1 -shortest -vf scale=1280:1280 result.flv
		const ffmpeg = createFFmpeg({
			log: true,
			progress: ({ ratio }) => {
				progressRatio = ratio;
			},
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
		isLoading = false;
		progressRatio = 0;
	}
</script>

<span>
	<button on:click={merge_image_and_audio} disabled={isLoading}>
		{#if isLoading}
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
		font-family: 'Fira Mono';
		font-size: 1rem;
	}
</style>

