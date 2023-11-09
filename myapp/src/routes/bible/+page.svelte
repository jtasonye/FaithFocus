<script>
	import { onMount } from 'svelte';

	let book = '';
	let chapter = '';

	// Reactive declaration to get the selected book and chapter from the store
	// let selectedBook = '';
	// let selectedChapter = '';

	// This is a variable to hold the entire fetched API chapter.
	let biblePassage = 'Loading passage...';

	// This variable is an array that stores individual verses fetched from the API.
	let versesArray = [];

	// This variable is any array that stores all the notes taken.
	let verseNotes = [];

	async function fetchPassage() {
		try {
			// Attempt to fetch the passage data from the API using the current book and chapter values.
			const response = await fetch(
				`https://labs.bible.org/api/?passage=${book}+${chapter}&type=json`
			);

			// Data is a variable that we can use to manipulate the whatever was fetched
			// Type json allows us to use variable names for book, chapter, etc
			const data = await response.json();
			const url = new URL(window.location.href);
			book = url.searchParams.get('book') || '';
			chapter = url.searchParams.get('chapter') || '';

			if (data && data.length > 0) {
				// Breaking chapter into verses and putting it in array to manipulate individually
				versesArray = data.map((verse) => ({
					// All keys are names we created, all values are from json file
					book: verse.bookname,
					chapter: verse.chapter,
					verseNumber: verse.verse,
					text: verse.text
				}));

				// Update the selected book and chapter to match the fetched passage.
				selectedBook = book;
				selectedChapter = chapter;

				// Convert the verses array into HTML, marking each verse clickable.
				biblePassage = versesArray
					.map((verse, index) => {
						return `<span class="clickable" data-index="${index}"> 
						<sup>${index + 1}</sup> ${verse.text} </span><br>`;
					})
					.join(' ');

				// Initialize an empty array to store notes for each verse
				verseNotes = versesArray.map(() => []);
				populateBookChaps();
			} else {
				biblePassage = 'No passage found.';
			}
		} catch (error) {
			biblePassage = 'Failed to load verse.';
		}
	}

	// Call the fetchPassage function when the component is mounted
	onMount(async () => {
		const url = new URL(window.location.href);
		book = url.searchParams.get('book') || '';
		chapter = url.searchParams.get('chapter') || '';

		await fetchPassage();
		// Attach click event listeners to verses
		addClickListeners();
		// Attach hover event listeners to verses
		addHoverListeners();
		// Attach click event listeners to notes
		addDeleteListeners();
	});

	function addClickListeners() {
		// Get all the verses from class name "clickable" and add a click event
		document.querySelectorAll('.clickable').forEach((item) => {
			item.addEventListener('click', handleVerseClick);
		});
	}

	function handleVerseClick(event) {
		// Get index number of the clicked verse
		const verseIndex = event.target.getAttribute('data-index');
		// Get the verse information in versesArray using verseIndex
		const selectedVerse = versesArray[verseIndex];

		// Add notes using window.prompt()
		const note = prompt(
			`What note would you like to add for ${selectedVerse.book} ${selectedVerse.chapter}:${selectedVerse.verseNumber} ?`
		);

		// Check to see that after removing the the unnecessary spaces, the input is not empty
		if (note != null && note.trim() != ' ') {
			// Add the note into the verseNotes array
			verseNotes[verseIndex].push(`${selectedVerse.book} 
			${selectedVerse.chapter}:${selectedVerse.verseNumber} - ${note}`);
			updateNotesPanel();
		}
		console.log(selectedVerse);
		console.log(note);
	}

	function handleVerseHover(event) {
		// Change background color when the verse is hovered over
		event.target.style.backgroundColor = 'lightgrey';
		// Change the mouse cursor into a point
		event.target.style.cursor = 'pointer';
	}

	function handleVerseMouseOut(event) {
		// Reset background color when the mouse leaves the verse
		event.target.style.backgroundColor = 'initial';
	}

	function addHoverListeners() {
		document.querySelectorAll('.clickable').forEach((item) => {
			item.addEventListener('mouseover', handleVerseHover);
			item.addEventListener('mouseout', handleVerseMouseOut);
		});
	}

	function updateNotesPanel() {
		const notesBody = document.getElementById('notes-list');
		if (notesBody !== null) {
			// Initialize an empty string to store the HTML content
			let notesHTML = '';

			// Loop through verseNotes to generate the HTML for notes
			verseNotes.forEach((notes, verseIndex) => {
				notes.forEach((note, noteIndex) => {
					// Concatenate each note's HTML without adding a separator
					notesHTML += `<div data-index="${verseIndex}">
                    <p note-index="${noteIndex}">${note}</p>
                    <button class="edit">Edit Note</button>
                    <button class="delete" data-index="${noteIndex}">Delete Note</button>
                	</div>`;
				});
			});

			// Set the inner HTML of notesBody to the generated notesHTML
			notesBody.innerHTML = notesHTML;
			addDeleteListeners();
		}
	}

	function addDeleteListeners() {
		// Get all the verses from class name "clickable" and add a click event
		document.querySelectorAll('.delete').forEach((item) => {
			item.addEventListener('click', handleDeleteClick);
		});
	}

	function handleDeleteClick(event) {
		const noteIndex = event.target.getAttribute('data-index');
		const selectedVerseIndex = event.target.parentNode.getAttribute('data-index');
		const selectedNote = verseNotes[selectedVerseIndex];

		// Remove the note based on the provided index
		selectedNote.splice(noteIndex, 1);
		updateNotesPanel();
	}

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
		const selectedBook = document.getElementById('books').value;
		const selectedChapter = document.getElementById('chapters').value;
		// Make sure both selects are inputed
		if (selectedBook && selectedChapter) {
			window.location.href = `/bible?book=${selectedBook}&chapter=${selectedChapter}`;
			// window.location.href = `/bible`;
		} else {
			alert('Please select both a book and a chapter.');
		}
	}
</script>

<div class="page-container">
	<div id="bible-passage">
		<header>
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

				<div class="search-button">
					<button type="button" id="search" on:click={searchButton}>Search</button>
				</div>
			</div>
		</header>

		<!-- @html ignores inline css styling and outputs variable -->
		<p id="passage-body">{@html biblePassage}</p>
	</div>

	<div id="notes-panel">
		<header>
			<p id="notes-header">Your Notes</p>
		</header>

		<div id="notes-list">
			<!-- Notes will be dynamically added here -->
		</div>
	</div>
</div>

<style>
	.select-book-chapter {
		margin-top: 20px;
		background-color: #edede9;
	}

	.align {
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 10px;
	}

	.bible-books select,
	.bible-chapters select,
	.search-button button {
		margin: 0;
		height: 40px;
	}

	.search-button {
		display: flex;
		align-items: center;
	}

	.page-container {
		display: flex;
		height: 85vh; /* Set the height of the container */
		border-radius: 15px;
		min-width: 520px;
	}

	/* Styles API fetched verses */
	#passage-body {
		letter-spacing: 1px;
	}

	#bible-passage,
	#notes-panel {
		width: 50%; /* Divids container into two equal parts */
		height: 100%; /* Fills entire height of the container */
		box-sizing: border-box; /* Include borders in width calculation */
		overflow: auto; /* Enable scrolling if content overflows */
		border-radius: 15px;
	}

	#bible-passage {
		border-right: 3px solid #ccc;
		border-top: 3px solid #ccc;
		border-bottom: 3px solid #ccc;
		background-color: #edede9;
		text-align: center;
	}

	#notes-panel {
		border-left: 3px solid #ccc;
		border-top: 3px solid #ccc;
		border-bottom: 3px solid #ccc;
		background-color: #d4ccc3;
	}

	#notes-panel header {
		background-color: #d9d9d9;
		border: 1px solid #d9d9d9;
	}

	#bible-passage header,
	#notes-panel header {
		height: 50px;
		width: 100%;
		text-align: center;
	}

	#notes-list {
		margin-left: 10px;
	}
</style>
