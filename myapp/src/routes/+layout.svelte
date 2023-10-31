<script>
	import Heading from './heading.svelte'
	import Footer from './footer.svelte'
</script>

<!-- Sets title of the page in tab -->
<svelte:head>
	<title>FaithFocus</title>
	<!-- <link rel="icon" href="https://fav.farm/" /> add logo to tab--> 
</svelte:head>

<Heading />
<slot />
<Footer />
