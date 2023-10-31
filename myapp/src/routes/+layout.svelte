<script>
	import Heading from './heading.svelte'
	import Footer from './footer.svelte'
</script>


<!-- Sets title of the page in tab -->
<svelte:head>
	<title>FaithFocus</title>
</svelte:head>

<Heading />
<slot />
<Footer />

