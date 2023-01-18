<script lang="ts">
	export let file: File | undefined;
	export let inputId = 'file';
	export let text = '';
	export let accept: 'image/*' | 'audio/*' | 'video/*' | 'image/*,audio/*,video/*' | undefined =
		undefined;
	export let subtitle: string | undefined = undefined;

	let files: FileList;

	$: console.log(files);
	$: file = files ? files[0] : undefined;
</script>

<div class="dropzone">
	<div class="dropzone__input">
		<input bind:files type="file" name={inputId} id={inputId} {accept} />
		<label for={inputId}>
			{#if files}
				{files[0].name}
			{:else}
				Choose a {text ? text + ' ' : ''}file
			{/if}
		</label>

		{#if subtitle}
			<span class="dropzone__subtitle">{subtitle}</span>
		{/if}
	</div>
</div>

<style>
	.dropzone {
		height: 6vh;
	}
	input {
		display: none;
	}

	label {
		padding: 1.5rem;
		border: 2px dashed #364fc7;
		border-radius: 15px;
		color: #364fc7;

		background-color: #eef4fb;

		font-size: large;

		/* don't break */
		white-space: nowrap;
	}

	.dropzone__subtitle {
		display: block;
		font-size: medium;
		text-align: center;
	}
</style>
