<script lang="ts">
	import Heading from './heading.svelte'
	import Footer from './footer.svelte'
    import '/src/app.css'
</script>


<!-- Sets title of the page in tab -->
<svelte:head>
	<title>FaithFocus</title>
</svelte:head>

<Heading />
<slot />
<Footer />

