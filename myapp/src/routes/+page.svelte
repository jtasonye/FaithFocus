<script>
	import { onMount } from 'svelte';

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

	// Call the fetchVerse function when the component is mounted
	onMount(fetchVerse);

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
        Revalation: 22
    };

    let sortOrder = 'Traditional'; // Added variable to store the sort order

    // Reactive statement to update the order of the books based on sortOrder
    $: orderedBooks = sortOrder === 'Traditional' ? Object.keys(bible) : 
    Object.keys(bible).sort();

    // Function to update sortOrder when radio buttons change
    // @ts-ignore
    function updateSortOrder(order) {
        sortOrder = order;
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
    function populateBookChaps(){
        allChaps = [];
        const selectedBookChaps = bible[selectedBook];
        if (selectedBookChaps)  {
            for(let i = 1; i <= selectedBookChaps; i++){
                allChaps.push(`${i}`);
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
            console.log("Selected Chapter:", selectedChapter);
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
            console.log("Selected Book:", selectedBook);
        }
    }

    function searchButton() {
        const selectedBook = document.getElementById('books').value;
        const selectedChapter = document.getElementById('chapters').value;
        // Make sure both selects are inputed
        if (selectedBook && selectedChapter) {
            window.location.href=`/bible?book=${selectedBook}&chapter=${selectedChapter}`;
            // window.location.href = `/bible`;
        } else {
            alert('Please select both a book and a chapter.');
        }
    }

</script>

<div class="page-wrap">
	<div class="page-content">
		<main>
            <img class="page-bg" src="/src/lib/images/book.jpg" alt="background-image" />
			<div class="title">
                <div class="shade" >
                    <h1>Welcome To Faith Focus</h1>
				    <h2>Study The Word Of God By Taking Notes</h2>
                </div>
			</div>
		</main>
	</div>

    <div class="select-book-chapter">
        <div id = "select-header">Begin Reading</div>

            <div class="align">
                <div class="bible-order">
                    <label for="trad">Trad.</label>
                    <input
                        id="trad"
                        type="radio"
                        value="Traditional"
                        name="radio-button"
                        checked
                        on:change={() => updateSortOrder('Traditional')}	
                    />
                    <label>Alph.</label>
                    <input
                        id="alph"
                        type="radio"
                        value="Alphabetical"
                        name="radio-button"
                        on:change={() => updateSortOrder('Alphabetical')}
                    />
                </div>

                <div class="bible-books">
                    <select id="books" bind:value={selectedBook} on:change|preventDefault={handleBookChange}>
                        <option value="" disabled selected>Select a book</option>
                        {#each orderedBooks as book}
                            <option value={book}>{book}</option>
                        {/each}
                    </select>
                </div>

                <div class="vertical"></div>

                <div class="bible-chapters">
                    <select id="chapters" bind:value={selectedChapter} on:change|preventDefault={handleChapterChange}>
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

<style>
    .title{
        font-weight: 200;
		border: 1px solid black;
        /* background: rgba(0,0,0,0.6); */
        padding-bottom: 250px;
    }

    .shade{
        margin-top:40px;
        background: rgba(0,0,0,0.6);

        /* border-radius: 8px; */
    }

	.title p {
		font-size: 20px;
	}

	.select-book-chapter {
		margin-top: 20px;
        background-color: #EDEDE9;
	}

    .vertical {
        width: 1px;
        height: 88px;
        border-left: 1px solid #132C13;
        position:absolute;
        left: 50%;
    }

    #select-header{
        font-size: 20px;
        color: #132C13;
    }
	.title, .select-book-chapter {
		text-align: center;
        color: #D4CCC3;
        /* color: #89CFF0; */
	}

	/* Aligns divs vertically */
	.align {
		display: flex; /* Added flex to align items horizontally */
		align-items: center; /* Center items vertically */
		justify-content: center; /* Center items horizontally */
        padding-right: 55px;;
	}
	.bible-order {
        color: #132C13;
        text-shadow: 1px 1px black;
        font-size: 24px;
		padding-bottom: 20px; /* Align radio buttons with selects */
	}

	.bible-order input[type='radio'] {
		display: block; /* Display radio buttons on top of each other */
		margin: 10px; /* Space between top and bottom button */
	}
	select {
		height: 50px;
        width: 200px;
        margin: 40px;
		/* width: 165px; */
		/* margin: 15px; */
        color: white;
        text-align: center;
	}

    .bible-books select, .bible-chapters select{
        background-color: #132C13;
    }
	.search-button button {
		height: 30px;
		width: 300px;
        color: white;
        background-color: gray;
	}

	.bible-order input[type='radio']:hover, select:hover, .search-button button:hover {
		background-color: #7EA172;
        cursor: pointer;
	}

	select, .search-button button {
		font-size: 16px;
        outline:none;
        border: 1px solid #7EA172;
		border-radius: 40px;
        transition: background-color 0.3s, color 0.3s;
	}
    .bible-order input[type="radio"] {
        display: block; /* Display radio buttons on top of each other */
        margin: 10px; /* Space between top and bottom button */
    }

    .page-wrap {
        overflow: hidden;
        position: relative;
    }

    .page-bg {
        opacity: 0.9;
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%; /* Make sure the background covers the entire content */
        /* object-fit: cover; Maintain image aspect ratio */
        object-fit: cover;
        /* Add the blur effect */
        filter: blur(2px);
        -webkit-filter: blur(2px);
        z-index: -1;
    }

    .page-content {
        position: relative;
        z-index: 1;
    }
	#verse-of-the-day {
        color: #D4CCC3;
        /* color:#89CFF0; */
		padding: 20px;
		background-color: rgb(39, 34, 34);
		border-radius: 15px;
		margin: 20px;
	}
</style>
