<script>
	import { onMount } from 'svelte';

	import { selectedBook , selectedChapter } from '/myapp/src/routes/+page.svelte'

	// let selectedBook = '';
	// console.log("BP Selected book: ", selectedBook);

    // let selectedChapter = '';
	// console.log("BP Selected chapter: ", selectedChapter);


	let verseText = 'Loading verse...';
	
	// Function to fetch the verse of the day
	async function fetchPassage() {
    fetch('https://labs.bible.org/api/?passage=John+4&type=json')

	// fetch('https://labs.bible.org/api/?passage=${selectedBook}+{selectedChapter}+&type=json')

        .then((response) => response.json())
        .then((data) => {
            if (data && data.length > 0) {
				// Variable to put the verse number in front of the verse
                const verses = data.map((verse, index) => {
                    return `${index + 1}. ${verse.text}`;


                }).join('\n');

				// These info will be in the buttons above in the header
                const bookName = data[0].bookname;
                const chapter = data[0].chapter;

                verseText = `${verses}`;
				// console.log(verseText);

            } else {
                verseText = 'No verse found.';
                // console.log(verseText);
            }
        })
        .catch((error) => {
            verseText = 'Failed to load verse.';
            // console.error(verseText, error);
        });
}

	// Call the fetchVerse function when the component is mounted
	onMount(fetchPassage);

</script>

<div class="bible-container">

	<div id="portrait-bible">
		<p> {verseText}</p>
	</div>

	<div id="notes-panel">
		<p>Need to figure out how anything works!</p>
	</div>
</div>

<style>
	.bible-container {
		display: flex;
		height: 100vh; /* Set the height of the container to the full viewport height */
	}

	#portrait-bible,#notes-panel {
		width: 50%; /* Divide the container into two equal parts */
		height: 100%; /* Fill the entire height of the container */
		background-color: #EDEDE9;
		border: 1px solid #ccc; /* Adding a border for visual separation */
		box-sizing: border-box; /* Include borders in width calculation */
		overflow: auto; /* Enable scrolling if content overflows */
		border-radius: 15px;
	}

	.all-books,.chapter-option {
		margin-left: 10px;
		padding-top: 10px;
		float: left;
		display: inline;
		width: 25%;
	}
</style>
