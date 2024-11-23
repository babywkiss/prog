<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>

<div class="h-full w-full p-3">
	{@render children()}
</div>
