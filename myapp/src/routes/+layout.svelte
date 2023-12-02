<script lang="ts">
	import Heading from './heading.svelte'
    import '/src/app.css'
</script>


<!-- Sets title of the page in tab -->
<svelte:head>
	<title>Faith Focus</title>
</svelte:head>

<Heading />
<slot />

