<script lang="ts">
	export let file: File | undefined;
	export let inputId = 'file';
	export let text = '';
	export let accept: 'image/*' | 'audio/*' | 'video/*' | 'image/*,audio/*,video/*' | undefined = undefined;

	let files: FileList;

	$: console.log(files);
	$: file = files ? files[0] : undefined;
</script>

<div class="dropzone">
	<div class="dropzone__input">
		<input
			bind:files
			type="file"
			name={inputId}
			id={inputId}
			accept={accept}
		/>
		<label for={inputId}>
			{#if files}
			{files[0].name}
			{:else}
			Choose a {text ? text + ' ' : ''}file
			{/if}
		</label>


	</div>
</div>

<style>
	input {
		display: none;
	}

	label {
		padding: 1rem;
		border: 2px dashed #364fc7;
		border-radius: 15px;
		color: #364fc7;

		background-color: #eef4fb;
	}
</style>
