<script>
	import { onMount } from 'svelte';

	// Initialize the book and chapter variables to get parameters from window.location.href.
	let book = '';
	let chapter = '';

	// This is a variable to hold the entire fetched API chapter.
	let biblePassage = 'Loading passage...';

	// @ts-ignore
	// This variable is an array that stores individual verses fetched from the API.
	let versesArray = [];

	// @ts-ignore
	// This variable is an array that stores all the notes taken.
	let verseNotes = [];

	async function fetchPassage() {
		try {
			// Fetch the passage data from the API using the current book and chapter values.
			const response = await fetch(
				`https://labs.bible.org/api/?passage=${book}+${chapter}&type=json`
			);

			/* 
			Data is a variable that we can use to manipulate the whatever was 
			fetched and Type json allows us to use variable names for book, chapter, etc.
			*/
			const data = await response.json();

			/*
			Create a new URL object that will be used to get the book and 
			chapter values from the previous page.
			*/
			const url = new URL(window.location.href);

			/* 
			Extract the value of the 'book' parameter from the URL.
			If the book or chapter parameter is not present, the values on 
			left side will be defaulted to an empty string.
			*/
			book = url.searchParams.get('book') || '';
			chapter = url.searchParams.get('chapter') || '';

			if (data && data.length > 0) {
				/* 
				Break the fetched passage into verses and put them in an array
				so that they can be manipulated individually.
				*/ //@ts-ignore
				versesArray = data.map((verse) => ({
					// All keys are names we create, all values are from json file.
					book: verse.bookname,
					chapter: verse.chapter,
					verseNumber: verse.verse,
					text: verse.text
				}));

				/* 
				Update the variables selectedBook and selectedChapter to match 
				the parameters passed from the previous page. 
				*/
				selectedBook = book;
				selectedChapter = chapter;

				/* 
				Make each verse clickable by converting the variable versesArray
				into HTML and add an index to differentiate the verses.
				*/
				biblePassage = versesArray
					// @ts-ignore
					.map((verse, index) => {
						return `<span class="clickable" data-index="${index}"> 
						<sup>${index + 1}</sup> ${verse.text} </span><br>`;
					})
					.join(' ');

				/* 
				Initialize the variable verseNotes as an array with the same
				length as versesArray. For each verse in verseArray, an empty
				array is created and assigned to the correct position in the 
				verseNotes array.
				*/
				verseNotes = versesArray.map(() => []); 

				// Update the avaliable chapters based on the selected book.
				populateBookChaps();
			} else {
				biblePassage = 'No passage found.';
			}
		} catch (error) {
			biblePassage = 'Failed to load verse.';
		}
	}

	// Function to save notes to local storage.
	function saveNotesToLocalStorage() {

		const savedNotes = localStorage.getItem(`verseNotes-${book}-${chapter}`);

		if (savedNotes) {
			/*
			If there are existing notes, parse and merge them with the 
			current notes.
			*/
			const existingNotes = JSON.parse(savedNotes);

			// @ts-ignore
			verseNotes.forEach((notes, verseIndex) => {
				existingNotes[verseIndex] = existingNotes[verseIndex] || [];
				existingNotes[verseIndex] = existingNotes[verseIndex].concat(notes);
			});

			// Save the merged notes to local storage.
			localStorage.setItem(`verseNotes-${book}-${chapter}`, JSON.stringify(existingNotes));
		} 
		else{
			// @ts-ignore
			// If there are no existing notes, save the current notes directly.
			localStorage.setItem(`verseNotes-${book}-${chapter}`, JSON.stringify(verseNotes));
		}

  		updateNotesPanel();

		
		 // Update the notes in local storage for the /notes page.
		 updateNotesInSharedStorage();

		// Refresh the page after calling "updateNotesPanel()".
		location.reload();
	}


	// @ts-ignore
	// Function to delete a note from local storage.
	function deleteNoteFromLocalStorage(verseIndex, noteIndex) {
		// Retrieve existing notes from local storage.
		const savedNotes = localStorage.getItem(`verseNotes-${book}-${chapter}`);

		if (savedNotes) {
			const existingNotes = JSON.parse(savedNotes);
			existingNotes[verseIndex].splice(noteIndex, 1);
			localStorage.setItem(`verseNotes-${book}-${chapter}`, JSON.stringify(existingNotes));
		}
		// Refresh the page after deleting the note.
		location.reload();
	}

	/*
	Function to get notes from local storage and update the "verseNotes"
	variable with the loaded notes.
	*/
	function loadNotesFromLocalStorage() {
		const savedNotes = localStorage.getItem(`verseNotes-${book}-${chapter}`);
		if (savedNotes) {
			verseNotes = JSON.parse(savedNotes);
			updateNotesPanel();
		}
	}

	onMount(async () => {
		const url = new URL(window.location.href);
		book = url.searchParams.get('book') || '';
		chapter = url.searchParams.get('chapter') || '';

		// Load notes from local storage when the component is mounted.
		loadNotesFromLocalStorage();

		await fetchPassage();

		// Attach click event listeners to verses.
		addClickListeners();
		addHoverListeners();

		// Attach click event listeners to notes.
		addDeleteListeners();
		addNoteHoverListeners();

	});

	function addClickListeners() {
		// Get all the verses from class name "clickable" and add a click event.
		document.querySelectorAll('.clickable').forEach((item) => {
			item.addEventListener('click', handleVerseClick);
		});
	}

	// @ts-ignore
	function handleVerseClick(event) {
		const verseIndex = event.target.getAttribute('data-index');
		//@ts-ignore
		const selectedVerse = versesArray[verseIndex];

		const action = prompt(
			"What would you like to do with this verse?\n - Type 'h' to highlight the verse\n - Type 'u' to unhighlight the verse\n - Type 'n' to add a note."
		);

		if (action === 'h' || action === 'H') {
			highlightVerse(event.target);
		} else if (action === 'u' || action === 'U') {
			unhighlightVerse(event.target);
		} else if (action === 'n' || action === 'N') {
			addNoteForVerse(selectedVerse, verseIndex);
		}
	}

	//@ts-ignore
	function addNoteForVerse(selectedVerse, verseIndex) {
		//@ts-ignore
		// Check if a note already exists for this verse
		if (verseNotes[verseIndex].length > 0) {
			// Prompt the user to decide on overwriting the note or cancelling
			const overwrite = confirm(
				'A note already exists for this verse. Do you want to overwrite it?'
			);
			if (!overwrite) {
				return; // Exit the function if the user chooses not to overwrite
			}
		}

		// Prompt for the new note
		const note = prompt(
			`What note would you like to add for ${selectedVerse.book} ${selectedVerse.chapter}:${selectedVerse.verseNumber}?`
		);

		if (note != null && note.trim() != '') {

			// Get current date to use as a time stamp
			var today = new Date();
			var month = today.getMonth() + 1;
			var day = today.getDate();
			var year = today.getFullYear();
			var newDate = month + "-" + day + "-" + year;

			// Replace the existing note or add a new one
			verseNotes[verseIndex] = [
				`<em><sup> Note added: ${newDate}</sup></em> <br />${selectedVerse.book} ${selectedVerse.chapter}:${selectedVerse.verseNumber} - ${note}`
			];
			saveNotesToLocalStorage();
			updateNotesPanel();
		}
	}

	// Add a variable to store the selected highlight color.
	let selectedHighlightColor = '#ff9fae';

	//@ts-ignore
	 // Update the highlightVerse and unhighlightVerse functions.
	 function highlightVerse(verseElement) {
        verseElement.classList.add('highlighted');
        verseElement.style.backgroundColor = selectedHighlightColor;
    }

	//@ts-ignore
    function unhighlightVerse(verseElement) {
        verseElement.classList.remove('highlighted');
        verseElement.style.backgroundColor = '';
    }

	//@ts-ignore
	function handleHighlightColorChange(color) {
    	selectedHighlightColor = color;
  	}

	// @ts-ignore
    // Update the handleVerseHover and handleVerseMouseOut functions.
    function handleVerseHover(event) {
        if (!event.target.classList.contains('highlighted')) {
            event.target.style.backgroundColor = selectedHighlightColor;
        }
        event.target.style.cursor = 'pointer';
    }

    // @ts-ignore
    function handleVerseMouseOut(event) {
        if (!event.target.classList.contains('highlighted')) {
            event.target.style.backgroundColor = 'initial';
        }
        event.target.style.cursor = 'pointer';
    }

	function addHoverListeners() {
		document.querySelectorAll('.clickable').forEach((item) => {
			item.addEventListener('mouseover', handleVerseHover);
			item.addEventListener('mouseout', handleVerseMouseOut);
		});
	}

	// @ts-ignore
	function handleNoteHover(event) {
		const verseIndex = event.target.parentNode.getAttribute('data-index');
		const verseElement = document.querySelector(`.clickable[data-index="${verseIndex}"]`);

		if (verseElement) {
			const isUnderlined = window.getComputedStyle(verseElement).textDecoration === 'underline';
			//@ts-ignore
			verseElement.style.textDecoration = isUnderlined ? 'none' : 'underline';
		}
	}

	// @ts-ignore
	function handleNoteMouseOut(event) {
		const verseIndex = event.target.parentNode.getAttribute('data-index');
		const verseElement = document.querySelector(`.clickable[data-index="${verseIndex}"]`);

		if (verseElement) {
			const isUnderlined = window.getComputedStyle(verseElement).textDecoration === 'none';
			//@ts-ignore
			verseElement.style.textDecoration = isUnderlined ? 'underline' : 'none';
		}
	}

	function addNoteHoverListeners() {
		document.querySelectorAll('.note').forEach((item) => {
			item.addEventListener('mouseover', handleNoteHover);
			item.addEventListener('mouseout', handleNoteMouseOut);
		});
	}

	// Function to update the UI to reflect changes in the "verseNotes" variable.
	function updateNotesPanel() {
		const notesBody = document.getElementById('notes-list');

		if (notesBody !== null) {
			// Initialize an empty string to store the HTML content.
			let notesHTML = '';

			// @ts-ignore
			// Loop through verseNotes to dynamically generate the HTML for notes.
			verseNotes.forEach((notes, verseIndex) => {
				// @ts-ignore
				notes.forEach((note, noteIndex) => {
					// Concatenate each note's HTML without adding a separator.
					notesHTML += 
					`<div class="note" data-index="${verseIndex}">
            		   <p note-index="${noteIndex}">${note}</p>
            		   <button class="edit" data-index="${noteIndex}">Edit Note</button>
            		   <button class="delete" data-index="${noteIndex}">Delete Note</button>
        			</div>`;
				});
			});

			// Set the inner HTML of "notesBody" to the generated "notesHTML"
			notesBody.innerHTML = notesHTML;

			// Select all the note elements.
			const noteElements = document.querySelectorAll('.note');

			// Loop through the selected note elements to apply styles
			noteElements.forEach((noteElement) => {
				// Check if noteElement is an HTMLElement
				if (noteElement instanceof HTMLElement) {
					// Apply styles to each note element as needed
					noteElement.style.border = '1px solid #ccc';
					noteElement.style.borderRadius = '8px';
					noteElement.style.padding = '10px';
					noteElement.style.margin = '10px 0';
					noteElement.style.backgroundColor = '#edede9';
					noteElement.style.boxShadow = '0 2px 4px rgba(0, 0, 0, 0.1)';
					noteElement.style.width = '85%';
				}
			});


			// Select all of the edit elements
			const editElements = document.querySelectorAll('.edit');

			// @ts-ignore
			function handleEditHover(event) {
				// event.target.style.backgroundColor = '#AFE1AF';
				event.target.style.backgroundColor = '#b1b9ae';
				event.target.style.cursor = 'pointer';
			}

			// @ts-ignore
			function handleEditMouseOut(event) {
				event.target.style.backgroundColor = 'var(--notesbgcolor)';
			}

			// Loop through the selected note elements to apply styles.
			editElements.forEach((editElement) => {
				editElement.addEventListener('mouseover', handleEditHover);
				editElement.addEventListener('mouseout', handleEditMouseOut);
				// Check if noteElement is an HTMLElement
				if (editElement instanceof HTMLElement) {
					editElement.style.color = '#353935';
					editElement.style.backgroundColor = 'var(--notesbgcolor)';
					editElement.style.borderRadius = '40px';
					editElement.style.marginBottom = '15px';
					editElement.style.fontFamily = 'Varela Round, sans-serif';
				}
			});


			// Select all the delete elements.
			const deleteElements = document.querySelectorAll('.delete');

			// @ts-ignore
			function handleDeleteHover(event) {
				event.target.style.backgroundColor = '#FF8080';
				event.target.style.cursor = 'pointer';
			}

			// @ts-ignore
			function handleDeleteMouseOut(event) {
				event.target.style.backgroundColor = 'var(--notesbgcolor)';
			}

			// Loop through the selected note elements to apply styles
			deleteElements.forEach((deleteElement) => {
				deleteElement.addEventListener('mouseover', handleDeleteHover);
				deleteElement.addEventListener('mouseout', handleDeleteMouseOut);
				// Check if noteElement is an HTMLElement
				if (deleteElement instanceof HTMLElement) {
					deleteElement.style.color = '#353935';
					deleteElement.style.backgroundColor = 'var(--notesbgcolor)';
					deleteElement.style.borderRadius = '40px';
					deleteElement.style.fontFamily = 'Varela Round, sans-serif';
				}
			});

			addDeleteListeners();
			addEditListeners();
		}
		 // Update the notes in local storage for the /notes page.
		 updateNotesInSharedStorage();
	}

	
	// Function to update the notes in shared storage (local storage).
	function updateNotesInSharedStorage() {
		//@ts-ignore
		localStorage.setItem('allVerseNotes', JSON.stringify(verseNotes));
	}

	function addDeleteListeners() {
		document.querySelectorAll('.delete').forEach((item) => {
			item.addEventListener('click', handleDeleteClick);
		});
	}

	function addEditListeners() {
		document.querySelectorAll('.edit').forEach((item) => {
			item.addEventListener('click', handleEditClick);
		});
	}


	// @ts-ignore
	function handleEditClick(event) {
		const noteIndex = event.target.getAttribute('data-index');
		const verseIndex = event.target.parentNode.getAttribute('data-index');
		// @ts-ignore
		const selectedVerse = versesArray[verseIndex];

		// Retrieve the existing note content from local storage
		const savedNotes = localStorage.getItem(`verseNotes-${book}-${chapter}`);
		let existingNotes = [];
		if (savedNotes) {
			existingNotes = JSON.parse(savedNotes);
		}

		// Get the existing note content
		const existingNote = existingNotes[verseIndex][noteIndex];
		const existingNoteContent = existingNote.split(' - ')[1];

		// Allow the user to edit the note content
		const newNoteContent = prompt(
			`Edit your note for ${selectedVerse.book} ${selectedVerse.chapter}:${selectedVerse.verseNumber}`,
			existingNoteContent
		);

		if (newNoteContent != null) {
			// Get current date to use as a time stamp
			var today = new Date();
			var month = today.getMonth() + 1;
			var day = today.getDate();
			var year = today.getFullYear();
			var newDate = month + "-" + day + "-" + year;

			// Update the note content in the existing note.
			existingNotes[verseIndex][noteIndex] = `<sup> Note edited: ${newDate}</sup> <br />${selectedVerse.book} 
			${selectedVerse.chapter}:${selectedVerse.verseNumber} - ${newNoteContent}`;

			// Save the updated notes back to local storage.
			localStorage.setItem(`verseNotes-${book}-${chapter}`, JSON.stringify(existingNotes));

			// Refresh the page after editing the note.
			location.reload();
		}
	}

	// @ts-ignore
	function handleDeleteClick(event) {
		const noteIndex = event.target.getAttribute('data-index');
		const selectedVerseIndex = event.target.parentNode.getAttribute('data-index');
		// @ts-ignore
		const selectedNote = verseNotes[selectedVerseIndex];

		// Remove the note based on the provided index
		selectedNote.splice(noteIndex, 1);

		/*
		Call the deleteNoteFromLocalStorage function and update the notes in
		local storage after deleting a note.
		*/
		deleteNoteFromLocalStorage(selectedVerseIndex, noteIndex);
		updateNotesPanel();
	}

	// localStorage.clear();

	// Run in console to see what is in local storage 

 	// for (let i = 0; i < localStorage.length; i++) {
	// 	const key = localStorage.key(i);
	// 	const value = localStorage.getItem(key);
	// 	console.log(`${key}-${value}`);
  	// } 

	
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

	let sortOrder = 'Traditional'; // Added variable to store the sort order.

	// Reactive statement to update the order of the books based on sortOrder.
	$: orderedBooks = sortOrder === 'Traditional' ? Object.keys(bible) : Object.keys(bible).sort();

	// Function to update sortOrder when radio buttons change.
	//@ts-ignore
	function updateSortOrder(order) {
		sortOrder = order;

		// Update button styles based on sortOrder.
		const tradButton = document.getElementById('trad');
      	const alphButton = document.getElementById('alph');

		if (sortOrder === 'Traditional') {
			// @ts-ignore
        	tradButton.style.backgroundColor = 'var(--slctcolor)'; 
			// @ts-ignore
        	alphButton.style.backgroundColor = '';
      	} else {
			  // @ts-ignore
        	alphButton.style.backgroundColor = 'var(--slctcolor)'; 
			// @ts-ignore
        	tradButton.style.backgroundColor = ''; 
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

	/*
	Function that takes the selected book from the bible object
	and retrieves the number of chapters to put into the allChaps array.
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
			searchButton();
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
			searchButton();
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
		} 
		else {
			alert('Please select both a book and a chapter.');
		}
	}

	// Function to navigate to the previous chapter
	function goToPreviousChapter() {
		// Extract the current chapter number string and convert it to an integer.
		const currentChapterNumber = parseInt(selectedChapter.split(' ')[1]);

		// If the current chapter isn't the first chapter, go back to the previous chapter.
		if (currentChapterNumber > 1) {
			const newChapterNumber = currentChapterNumber - 1;
			selectedChapter = `Chapter ${newChapterNumber}`;
			window.location.href = `/bible?book=${selectedBook}&chapter=${selectedChapter}`;
		}
		else {
			// Find the index of the currently selected book in the 'orderedBooks' array.
			const bookIndex = orderedBooks.indexOf(selectedBook);
			// Checks if the current book is not the first book.
			if (bookIndex > 0) {
				// Move to the previous book.
				selectedBook = orderedBooks[bookIndex - 1];
				
				// Set the chapter to the first chapter of the previous book
				selectedChapter = `Chapter 1`;
				window.location.href = `/bible?book=${selectedBook}&chapter=${selectedChapter}`;
			}
		}
	}

	// Function to navigate to the next chapter.
	function goToNextChapter() {
		// Extract the current chapter number string and convert it to an integer.
		const currentChapterNumber = parseInt(selectedChapter.split(' ')[1]);
		const totalChapters = allChaps.length;

		if (currentChapterNumber < totalChapters) {
			const newChapterNumber = currentChapterNumber + 1;
			selectedChapter = `Chapter ${newChapterNumber}`;
			window.location.href = `/bible?book=${selectedBook}&chapter=${selectedChapter}`;
		} 
		// Check if the current chapter is the last chapter of the book.
		else {
			// Find the index of the currently selected book in the 'orderedBooks' array.
			const bookIndex = orderedBooks.indexOf(selectedBook);
			// Check if the current book is not the last book of the Bible.
			if (bookIndex < orderedBooks.length - 1) {
				// Move to the next book if available.
				selectedBook = orderedBooks[bookIndex + 1];
				// Set the current chapter to the first chapter of the next book.
				selectedChapter = `Chapter 1`;
				window.location.href = `/bible?book=${selectedBook}&chapter=${selectedChapter}`;
			}
		}
	}

</script>

<div class="page-container">
	<div id="bible-passage">
		<header>
			<div class="align">

				<div class="bible-order">
					<button id="trad" on:click={() => updateSortOrder('Traditional')}>TRD</button>
					<button id="alph" on:click={() => updateSortOrder('Alphabetical')}>ALPH</button>
				</div>

				<div class="stack">
					<div class="bible-books">
						<select
							id="books"
							bind:value={selectedBook}
							on:change|preventDefault={handleBookChange}
						>
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
				</div>
			</div>
		</header>

		<!-- @html ignores inline css styling and outputs variable -->
		<p id="passage-body">{@html biblePassage}</p>

		<div class="navigation-container">
			<div class="navigation-buttons">
			  <button on:click={goToPreviousChapter}>&lt; Prev</button>
			  <button on:click={goToNextChapter}>Next &gt;</button>
			</div>
		</div>

		<p id="citation">
			<em>
				“The Scriptures quoted are from the NET Bible® https://netbible.com 
				copyright ©1996, 2019 used with permission from Biblical Studies Press, 
				L.L.C. All rights reserved”.
			</em>
		</p>
	</div>

	<div id="notes-panel">
		<header>
			<p id="notes-header">Notes: Click on a verse to start</p>
			<!-- <p>Click on a verse to start taking notes !</p> -->
			<div class="highlight-color-options">
				<label>
				  <!-- Highlight Color: -->
				  <div class="color-buttons">
					<button class="color-button" style="background-color: #ff9fae" on:click={() => handleHighlightColorChange('#ff9fae')}></button>
					<button class="color-button" style="background-color: #ffc87a" on:click={() => handleHighlightColorChange('#ffc87a')}></button>
					<button class="color-button" style="background-color: #fde995" on:click={() => handleHighlightColorChange('#fde995')}></button>
					<button class="color-button" style="background-color: #a6e1c5" on:click={() => handleHighlightColorChange('#a6e1c5')}></button>
					<button class="color-button" style="background-color: #a7e0f6" on:click={() => handleHighlightColorChange('#a7e0f6')}></button>
					<button class="color-button" style="background-color: #e1a7fb" on:click={() => handleHighlightColorChange('#e1a7fb')}></button>
				  </div>
				</label>
			  </div>
		</header>

		<div id="notes-list">
			<!-- Notes will be dynamically added here -->
		</div>
	</div>
</div>

<footer>
</footer>


<style>
	@import url('https://fonts.cdnfonts.com/css/varela-round-3');

	#citation {
		text-align: center;
		width: 90%;
		margin-left: 5%;
	}

	.navigation-buttons {
        display: flex;
        justify-content: space-between;
        margin-bottom: 10px;
		margin: 0 10px 120px 20px;
    }

    .navigation-buttons button {
        font-size: 13px;
		font-family: 'Varela Round', sans-serif;
        background-color: var(--hovcolor);
        color: white;
        padding: 8px 12px;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        transition: background-color 0.3s;
    }

    .navigation-buttons button:hover {
        background-color: var(--slctcolor);
    }

	.navigation-container {
		position: fixed;
		bottom: 0;
		left: 0;
		width: 61%;
		z-index: 1000;
	}

	.color-buttons {
		display: flexbox;
	}

	.color-button {
		background-color: transparent;
		border: none;
		border-radius: 50%;
		width: 30px;
		height: 30px;
		margin: 0 5px;
		cursor: pointer;
	}

	.highlight-color-options select {
		width: 150px;
	}

	footer {
		background-color: var(--hdrcolor);
		/* position:fixed; */
		left: 0;
		bottom: 0;
		width: 100%;
		height: 2.5vh;
		text-align: center;	
		z-index: 1;
	}
	.align {
		display: flex;
		align-items: center;
		justify-content: center;
		/* gap: 10px; */
	}

	.bible-order {
		margin-right: 0px;
		margin-left: 15px;
	}

	/*   Div for me   */
	.bible-order button {
		display: block; /* Display radio buttons on top of each other */
		margin: 5px; /* Space between top and bottom button */
		width: 70px;
		/* margin: 15px 70px 15px 70px; */
		/* margin: 15px 10px 0px -200px; */
	}
	select {
		/* width: 200px; */
		width: 170px;
		text-align: center;
		margin: 0;
		height: 40px;
		-webkit-appearance: none;
  		-moz-appearance: none;
  		text-indent: 5px;
	}
	
	.bible-order button:hover,
	select:hover,
	.search-button button:hover {
		/* background-color: var(--hovcolor); */
		background-color: var(--slctcolor);
		cursor: pointer;
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

	/* Style for highlighted verses */
    .highlighted {
        background-color: var(--highlight-color) !important;
    }
	.page-container {
		display: flex;
		height: 95vh; /* Set the height of the container */
		width: 100vw;
		border-radius: 15px;
	}

	/* Styles API fetched verses */
	#passage-body {
		letter-spacing: 1px;
		padding-bottom: 50px;
	}

	#bible-passage,
	#notes-panel {
		/* width: 50%; Divids container into two equal parts */
		height: 100%; /* Fills entire height of the container */
		box-sizing: border-box; /* Include borders in width calculation */
		overflow: auto; /* Enable scrolling if content overflows */
		border-radius: 15px;
	}

	#bible-passage {
		background-color: var(--biblebgcolor);
		text-align: center;
		width: 62%;
	}

	#notes-panel {
		background-color: var(--notesbgcolor);
		width: 38%;
	}

	#notes-panel header {
		font-family: 'Varela Round', sans-serif;
		background-color: var(--cadetgrey);
		border: 1px solid var(--cadetgrey);
		color: white;
	}

	#bible-passage header,
	#notes-panel header {
		width: 100%;
		text-align: center;
		padding-bottom: 40px;
	}

	#notes-list {
		margin-left: 10px;
	}

	.bible-books,
	.bible-chapters {
		display: inline-block;
		margin: 5px;
	}

	/* This ensures items fit in mobile view */
	@media (max-width: 654px) {
		.bible-books,
		.bible-chapters {
			display: block;
			width: 100%;
		}

		.stack {
			margin-right: 30px;
		}

		select {
			width: 140px;
		}

		.bible-order button {
			color: white;
			margin: 0px 0px 0px 5px;
		}

		#bible-passage header {
			height: 50px;
		}

		#notes-panel header {
			height: 115px;
		}

		.navigation-buttons {
			justify-content:space-between;
			margin: 0 5px 120px 9px;
		}

		.navigation-buttons button {
			font-size: 12px;
			padding: 11px 10px;
		}

		#citation {
			font-size: 15px;
			width: 85%;
			margin-left: 9%;
		}
	}


	/* Adds smoother transition from larger to smaller screens */
	@media (min-width: 630px){
		/* 656px */
		#bible-passage header {
			height: 30px;
		}

		#notes-panel header {
			height: 78px;
		}
	}
</style>
