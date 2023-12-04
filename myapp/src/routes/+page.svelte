<script>
	import { onMount } from 'svelte';
    import bgImage from '/src/lib/images/road.png';

	let verseOfTheDay = 'Loading verse...';

	// Function to fetch the verse of the day
	function fetchVerse() {
		fetch('https://labs.bible.org/api/?passage=votd&type=json')
			.then((response) => response.json())
			.then((data) => {
				// The API might return a list of verses, so we ensure we're accessing the first one.
				if (data && data.length > 0) {
					// Construct the verse with the text and reference
					verseOfTheDay = `${data[0].bookname} ${data[0].chapter}:${data[0].verse} - ${data[0].text}`;
				} else {
					// Handle the case where data might not be in the expected format or is empty
					verseOfTheDay = 'No verse found.';
				}
			})
			.catch((error) => {
				// Handle any errors that occurred during the fetch
				verseOfTheDay = 'Failed to load verse.';
				// console.error(verseOfTheDay, error);
			});
	}

    onMount(async () => {
		await fetchVerse();
		bgImage;
	});

	/**
	 * @type {Object.<string, number>}
	 *
	 * This object holds all book names and chapters in key value pairs.
	 */
	let bible = {
		// Old Testament
		Genesis: 50,
		Exodus: 40,
		Leviticus: 27,
		Numbers: 36,
		Deuteronomy: 34,
		Joshua: 24,
		Judges: 21,
		Ruth: 4,
		'1 Samuel': 31,
		'2 Samuel': 24,
		'1 Kings': 22,
		'2 Kings': 25,
		'1 Chronicles': 29,
		'2 Chronicles': 36,
		Ezra: 10,
		Nehemiah: 13,
		Esther: 10,
		Job: 42,
		Psalms: 150,
		Proverbs: 31,
		Ecclesiastes: 12,
		'Song of Songs': 8,
		Isaiah: 66,
		Jeremiah: 52,
		Lamentations: 5,
		Ezekiel: 48,
		Daniel: 12,
		Hosea: 14,
		Joel: 3,
		Amos: 9,
		Obadiah: 1,
		Jonah: 4,
		Micah: 7,
		Nahum: 3,
		Habakkuk: 3,
		Zephaniah: 3,
		Haggai: 2,
		Zechariah: 14,
		Malachi: 4,

		// New Testament
		Matthew: 28,
		Mark: 16,
		Luke: 24,
		John: 21,
		Acts: 28,
		Romans: 16,
		'1 Corinthians': 16,
		'2 Corinthians': 16,
		Galatians: 6,
		Ephesians: 6,
		Philippians: 4,
		Colossians: 4,
		'1 Thessalonians': 5,
		'2 Thessalonians': 3,
		'1 Timothy': 6,
		'2 Timothy': 4,
		Titus: 3,
		Philemon: 1,
		Hebrews: 13,
		James: 5,
		'1 Peter': 5,
		'2 Peter': 3,
		'1 John': 5,
		'2 John': 1,
		'3 John': 1,
		Jude: 1,
		Revelation: 22
	};

	let sortOrder = 'Traditional'; // Added variable to store the sort order

	// Reactive statement to update the order of the books based on sortOrder
	$: orderedBooks = sortOrder === 'Traditional' ? Object.keys(bible) : Object.keys(bible).sort();

	// Function to update sortOrder when radio buttons change
	// @ts-ignore
	function updateSortOrder(order) {
		sortOrder = order;

		// Update button styles based on sortOrder
		const tradButton = document.getElementById('trad');
      	const alphButton = document.getElementById('alph');

		if (sortOrder === 'Traditional') {
			// @ts-ignore
        	tradButton.style.backgroundColor = 'var(--slctcolor)';
        	// @ts-ignore
			alphButton.style.backgroundColor = ''; // Reset Alphabetical button color
      	} else {
			// @ts-ignore
        	alphButton.style.backgroundColor = 'var(--slctcolor)';
        	// @ts-ignore
			tradButton.style.backgroundColor = ''; // Reset Traditional button color
      	}
	}

	/**
	 * @type {string}
	 *
	 * This variable holds selected book value to be passed into the API call.
	 */
	export let selectedBook = '';

	/**
	 * @type {string}
	 *
	 * This variable holds the selected chapter to be passed into the API call.
	 */
	export let selectedChapter = '';

	/**
	 * @type {string[]}
	 *
	 * This variable holds all the chapter numbers of each book.
	 */
	let allChaps = [];

	/** Function that takes the selected book from the bible object
	 *  and retrieves the number of chapters to put into the allChaps array.
	 */
	function populateBookChaps() {
		allChaps = [];
		const selectedBookChaps = bible[selectedBook];
		if (selectedBookChaps) {
			for (let i = 1; i <= selectedBookChaps; i++) {
				allChaps.push(`Chapter ${i}`);
				// console.log(allChaps);
			}
		}
	}

	/**
	 * @param {Event} event - The event object triggered on chapter selection.
	 *
	 * This function handles the change event for selecting a chapter.
	 */
	function handleChapterChange(event) {
		const target = event.target;
		if (target instanceof HTMLSelectElement) {
			const selectedChapter = target.value;
			console.log('Selected Chapter:', selectedChapter);
		}
	}

	/**
	 * @param {Event} event - The event object triggered on book selection.
	 *
	 * Handles the change event for selecting a book.
	 * This function sets the selected book value and populates its chapters.
	 */
	function handleBookChange(event) {
		const target = event.target;
		if (target instanceof HTMLSelectElement) {
			const selectedBook = target.value;
			populateBookChaps();
			console.log('Selected Book:', selectedBook);
		}
	}

	function searchButton() {
		// @ts-ignore
		const selectedBook = document.getElementById('books').value;
		// @ts-ignore
		const selectedChapter = document.getElementById('chapters').value;
		// Make sure both selects are inputed
		if (selectedBook && selectedChapter) {
			window.location.href = `/bible?book=${selectedBook}&chapter=${selectedChapter}`;
		} else if (selectedBook && !selectedChapter){
			alert('Please select a chapter.');
		}
		else {
			alert('Please select both a book and a chapter.');
		}
	}
</script>

<div class="page-wrap">
	<img class="page-bg" src="{bgImage}" alt="Image of road surrounded by a vast forrest with a cloudy
	sky" />

	<div class="page-content">

		<div class="title">
			<h1><strong>Embark on Your Spiritual Journey with Faith Focus</strong></h1>
			<h2><strong>Select a book and chapter to get started</strong></h2>
		</div>

		<div class="selection-container">
	
				<div class="align">
					<div class="bible-order">
						
						<div class="tooltip">
							<button id="trad" on:click={() => updateSortOrder('Traditional')}>TRD</button>
							<span class="tooltiptext"> Traditional Sort</span>
						</div>
		
						<div class="tooltip">
							<button id="alph" on:click={() => updateSortOrder('Alphabetical')}>ALPH</button>
							<span class="tooltiptext">Alphabetical Sort</span>
						</div>
		
					</div>
		
					<div class="bible-books">
						<select id="books" bind:value={selectedBook} on:change|preventDefault={handleBookChange}>
							<option value="" disabled selected>Select a book</option>
							{#each orderedBooks as book}
								<option value={book}>{book}</option>
							{/each}
						</select>
					</div>
		
					<div class="vertical" />
		
					<div class="bible-chapters">
						<select
							id="chapters"
							bind:value={selectedChapter}
							on:change|preventDefault={handleChapterChange}
						>
							<option value="" disabled selected>Select a chapter</option>
							{#each allChaps as chapter}
								<option value={chapter}>{chapter}</option>
							{/each}
						</select>
					</div>
				</div>
	
			<div class="search-button">
				<button type="button" id="search" on:click={searchButton}>Search</button>
			</div>
	
			<div id="verse-of-the-day">
				<h2>Verse of the Day</h2>
				<p>{verseOfTheDay}</p>
			</div>
		</div>
	</div>
</div>
<footer>
</footer>



<style>
	@import url('https://fonts.cdnfonts.com/css/varela-round-3');

	footer {
		background-color: var(--hdrcolor);
		position:fixed;
		left: 0;
		bottom: 0;
		width: 100%;
		/* height: 20px; */
		height: 2.5vh;
		text-align: center;	
		z-index: 1;
	}
	.title {
		/* text-align: center; */
		color: var(--gold);
		/* color: #d7ceaf; */
		font-weight: 900;
		font-family: 'Varela Round', sans-serif;
	}

	.title h1 {
		/* margin-top: 60px; */
		font-size: 50px;
		/* margin-top: 290px; */
		margin-top: 30vh;
		margin-bottom: 0vh;
		text-shadow: 3px 1px black;
	}

	.title h2 {
		/* margin-top: 15px; */
		margin-top: 2vh;
		font-size: 30px;
		text-shadow: 3px 1px black;
	}

	.title, .selection-container {
		text-align: center;
	}

	.selection-container {
		/* margin-top: 20px;
		background-color: #eee5cf; */
	}

	.align {
		display: flex;
		/* Align horizontally */
		align-items: center; 
		/* Align vertically */
		justify-content:center;
		/* padding-right: 55px; */
		padding-right: 10vh;
	}

	.bible-order button {
		display: block;
		cursor: pointer;
		width: 70px;
		margin: 15px;
	}

	.tooltip {
      position: relative;
      cursor: pointer;
    }

    .tooltip .tooltiptext {
      visibility: hidden;
      width: 100px;
      background-color: #333;
      color: white;
      text-align: center;
      border-radius: 8px;
      padding: 5px;
      position: absolute;
      z-index: 1;
	  top: -5px;
  	  right: 105%;
      margin-left: -30px;
      opacity: 0;
      transition: opacity 0.3s;
	  font-size: 20px;
    }

    .tooltip:hover .tooltiptext {
      visibility: visible;
      opacity: 1;
    }


	select {
		text-align: center;
		height: 50px;
		width: 250px;
		margin: 0px 20px;
		-webkit-appearance: none;
  		-moz-appearance: none;
  		text-indent: 5px;
  		/* text-overflow: ''; */
	}

	.search-button button {
		height: 40px;
		width: 300px;
		/* margin: 20px 0 0 20px; */
		margin: 4vh 4.5vh 1vh 4vh;
	}

	select,
	.search-button button,
	.bible-order button {
		font-size: 16px;
		font-family: 'Varela Round', sans-serif;
		outline: none;
		border: 2px solid var(--hovcolor);
		border-radius: 40px;
		transition: background-color 0.3s, color 0.3s;
		color: white;
		background-color: var(--hdrcolor);
	}

	.bible-order button:hover,
	select:hover,
	.search-button button:hover {
		/* background-color: var(--hovcolor); */
		background-color: var(--slctcolor);
		cursor: pointer;
	}

	.page-wrap {
		overflow: hidden;
		position: relative;
		width: 100%;
		height: 100vh;
		padding-bottom: 150px;
  	}

	.page-bg {
		width: 100%;
		height: 100%;
		object-fit: cover;
		z-index: -1;
		position: absolute;
	}

	.page-content {
		position:relative;
		z-index: 1;
	}


	#verse-of-the-day {
		/* color: #d4ccc3; */
		/*
		padding: 20px;
		background-color: rgb(39, 34, 34);
		border-radius: 15px;
		margin: 150px;
		margin: 90px; */

		color: var(--slctcolor);
		/* color: #d7ceaf; */
		font-size: 20px;
		font-weight: 900;
		text-shadow: 2px 1px black;
		border-radius: 15px;
		/* margin: 110px; */
		margin: 14vh;
		background-color: rgb(39, 34, 34, 0.7);
	}

	@media (max-width: 730px) {
		.title h1 {
			margin-top: 100px;
			font-size: 50px;
		}

		.title h2 {
			font-size: 30px;
		}

		.bible-order {
			display: none;
		}

		.align {
			padding-right: 0px;
		}

		select {
			width: 155px;
			/* margin: 0px 10px 0 10px; */
		}

		.search-button button {
			margin: 5vh 4.5vh 0 5vh;
		}
		.page-bg {
			width: 100%;
      		height: 980px; 
		}

		#verse-of-the-day {
			margin: 7vh;
			font-size: 20px;
		}
	}

	/* iPhone SE */
	/* Max-width: if [device width] is less than or equal to, then apply style */
	@media (min-width: 0px) and (max-width: 380px) {
		.title h1 {
			font-size: 35px;
			margin-top: 100px;
		}

		.title h2 {
			font-size: 23px;
		}

		#verse-of-the-day {
			margin: 11vh;
			font-size: 15px;
		}
	}

</style>
