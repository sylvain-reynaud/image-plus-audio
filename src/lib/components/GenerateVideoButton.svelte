<script lang="ts">
	const { createFFmpeg, fetchFile } = FFmpeg;
	// import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';

	export let imageFile: File;
	export let audioFile: File;
	export let progressRatio = 0;

	export let isLoading = false;
	async function merge_image_and_audio() {
		isLoading = true;
		const finalFilename = `${removeExtension(audioFile.name)}.mp4`;
		const changedImage = await changeImageResolution(imageFile);
		console.log('changedImage:', changedImage);
		// ffmpeg -r 1 -loop 1 -i image.jpg -i audio.wav -acodec copy -r 1 -shortest -vf scale=1280:1280 result.flv
		const ffmpeg = createFFmpeg({
			log: true,
			progress: ({ ratio }) => {
				progressRatio = ratio;
			}
		});
		await ffmpeg.load();
		ffmpeg.FS('writeFile', changedImage.file.name, await fetchFile(changedImage.file));
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
			`scale=${changedImage.width}:${changedImage.height}`,
			finalFilename
		);

		const data = ffmpeg.FS('readFile', finalFilename);

		const url = URL.createObjectURL(new Blob([data.buffer], { type: 'video/mp4' }));

		const a = document.createElement('a');
		a.href = url;
		a.download = finalFilename;
		a.click();
		isLoading = false;
		progressRatio = 0;
	}

	type changedImage = { file: File; width: number; height: number };
	/* Change the image resolution,
	 * so that the video will match youtube resolution.
	 * If the image is more likely to be 4:3, then the video will be 4:3.
	 * If the image is more likely to be 16:9, then the video will be 16:9.
	 * Else, the video will not change.
	 */
	async function changeImageResolution(file: File): Promise<changedImage> {
		// get the image width and height
		const img = document.createElement('img');
		img.src = URL.createObjectURL(file);
		await new Promise((resolve) => {
			img.onload = resolve;
		});
		const width = img.width;
		const height = img.height;
		console.log('width:', width, 'height:', height);

		// calculate the nearest ratio
		const ratio = width / height;
		const ratio4_3_diff = Math.abs(ratio - 4 / 3);

		const ratio16_9_diff = Math.abs(ratio - 16 / 9);
		console.log(`ratio is ${ratio}`);
		console.log(`ratio4_3_diff is ${ratio4_3_diff}`);
		console.log(`ratio16_9_diff is ${ratio16_9_diff}`);

		// choose the nearest ratio supported by youtube
		const youtube16_9Ratios = [
			{ height: 360, width: 640 },
			{ height: 480, width: 854 },
			{ height: 720, width: 1280 },
			{ height: 1080, width: 1920 },
			{ height: 1440, width: 2560 },
			{ height: 2160, width: 3840 }
		];
		const youtube4_3Ratios = [
			{ height: 480, width: 640 },
			{ height: 720, width: 960 },
			{ height: 1080, width: 1440 },
			{ height: 1440, width: 1920 }
		];
		let youtubeRatio;
		if (ratio4_3_diff < ratio16_9_diff) {
			// calculate the nearest ratio
			let minDiff = Infinity;
			for (const ratio of youtube4_3Ratios) {
				const diff = Math.abs(ratio.height - height);
				if (diff < minDiff) {
					minDiff = diff;
					youtubeRatio = ratio;
				}
			}
		} else {
			// calculate the nearest ratio
			let minDiff = Infinity;
			for (const ratio of youtube16_9Ratios) {
				const diff = Math.abs(ratio.height - height);
				if (diff < minDiff) {
					minDiff = diff;
					youtubeRatio = ratio;
				}
			}
		}

		// resize the image
		const canvas = document.createElement('canvas');
		canvas.width = youtubeRatio?.width || width;
		canvas.height = youtubeRatio?.height || height;
		const ctx = canvas.getContext('2d');
		ctx?.drawImage(img, 0, 0, canvas.width, canvas.height);
		const dataURL = canvas.toDataURL('image/jpeg');
		const newFile: File = await fetch(dataURL)
			.then((r) => r.blob())
			.then((blobFile) => new File([blobFile], file.name, { type: file.type }));
		return { file: newFile, width: canvas.width, height: canvas.height };
	}

	function removeExtension(filename: string): string {
		const extIndex = filename.lastIndexOf('.');
		if (extIndex > 0) {
			filename = filename.substring(0, extIndex);
		}
		return filename;
	}
</script>

<span>
	<button on:click={merge_image_and_audio} disabled={isLoading}>
		{#if isLoading}
			Loading...
		{:else}
			Click here to generate the video
		{/if}
	</button></span
>

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
