<script lang="ts">
	export let id: string;
	export let text: string;
</script>

<button {id}>{text}</button>

<style>
	button {
		border: 0;
		border-radius: 0.25rem;
		background: #1e88e5;
		color: white;
		font-family: -system-ui, sans-serif;
		font-size: 1.5rem;
		line-height: 2;
		white-space: nowrap;
		text-decoration: none;
		padding: 2rem 2rem;
		margin: 0.25rem;
		cursor: pointer;
	}
</style>
