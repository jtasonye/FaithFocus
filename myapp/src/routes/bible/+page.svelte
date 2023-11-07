<script>
	import { onMount } from 'svelte';

	let selectedBook = "";
	// console.log("BP Selected book: ", selectedBook);
    let selectedChapter = "";
	// console.log("BP Selected chapter: ", selectedChapter);

	// This is a variable to hold the entire fetched API chapter.
	let biblePassage = "Loading passage...";

	// This variable is an array that stores individual verses fetched from the API.
	let versesArray = [];

	// This variable is any array that stores all the notes taken.
	let verseNotes = [];

	async function fetchPassage() {
		try {
			// Response holds the fetched API call
			// const response = await fetch(`https://labs.bible.org/api/?passage=+${selectedBook.trim()}+${selectedChapter.trim()}&type=json`);
			const response = await fetch("https://labs.bible.org/api/?passage=Genesis+1&type=json");

			// Data is a variable that we can use to manipulate the whatever was fetched
			// Type json allows us to use variable names for book, chapter, etc
			const data = await response.json();

			if (data && data.length > 0) {
				// Breaking chapter into verses and putting it in array to manipulate individually
				versesArray = data.map(verse => ({
					// All keys are names we created, all values are from json file
					book: verse.bookname,
					chapter: verse.chapter,
					verseNumber: verse.verse,
					text: verse.text
				}));
				biblePassage = versesArray.map((verse, index) => {
					return `<span class="clickable" data-index="${index}"> 
						<sup>${index + 1}</sup> ${verse.text} </span><br>`;
				}).join(" ");
				// Initialize an empty array to store notes for each verse
				verseNotes = versesArray.map( () => [] );
			} else{
				biblePassage = "No passage found.";
			}
		} catch (error){
			biblePassage = "Failed to load verse.";
		}
	}

	// Call the fetchPassage function when the component is mounted
	onMount(async () => {
		await fetchPassage();
		// Attach click event listeners to verses
		addClickListeners();
		// Attach hover event listeners to verses
		addHoverListeners();
		// Attach click event listeners to notes
		addDeleteListeners();
	});

	function addClickListeners(){
		// Get all the verses from class name "clickable" and add a click event
		document.querySelectorAll(".clickable").forEach(item => {
			item.addEventListener("click", handleVerseClick);
		});
	}

	function handleVerseClick(event){
		// Get index number of the clicked verse
		const verseIndex = event.target.getAttribute("data-index");
		// Get the verse information in versesArray using verseIndex
		const selectedVerse = versesArray[verseIndex];

		// Add notes using window.prompt()
		const note = prompt(`What note would you like to add for ${selectedVerse.book} ${selectedVerse.chapter}:${selectedVerse.verseNumber} ?`);

		// Check to see that after removing the the unnecessary spaces, the input is not empty
		if(note != null && note.trim() != " "){
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
    	event.target.style.backgroundColor = "lightgrey";
		// Change the mouse cursor into a point
		event.target.style.cursor = "pointer";
	}

	function handleVerseMouseOut(event) {
		// Reset background color when the mouse leaves the verse
		event.target.style.backgroundColor = "initial";
	}

	function addHoverListeners() {
		document.querySelectorAll(".clickable").forEach(item => {
			item.addEventListener("mouseover", handleVerseHover);
			item.addEventListener("mouseout", handleVerseMouseOut);
		});
	}
	
	function updateNotesPanel() {
		const notesBody = document.getElementById("notes-list");
		if (notesBody !== null) {
			// Initialize an empty string to store the HTML content
			let notesHTML = "";

			// Loop through verseNotes to generate the HTML for notes
			verseNotes.forEach((notes, verseIndex) => {
            notes.forEach( (note, noteIndex) => {
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

	function addDeleteListeners(){
		// Get all the verses from class name "clickable" and add a click event
		document.querySelectorAll(".delete").forEach(item => {
			item.addEventListener("click", handleDeleteClick);
		});
	}

	function handleDeleteClick(event) {
		const noteIndex = event.target.getAttribute("data-index");
		const selectedVerseIndex = event.target.parentNode.getAttribute("data-index");
		const selectedNote = verseNotes[selectedVerseIndex];

		// Remove the note based on the provided index
		selectedNote.splice(noteIndex, 1);
		updateNotesPanel();
	}

</script>

<div class="page-container">

	<div id="bible-passage">
		<header>
			<p id="passage-header"> This is temp header</p>
		</header>

		<!-- @html ignores inline css styling and outputs variable -->
		<p id="passage-body"> {@html biblePassage}</p>
	</div>

	<div id="notes-panel">
		<header>
			<p id="notes-header"> Your Notes - click on a verse to get started </p>
		</header>

		<div>
			<p id="notes-list"></p>
			<!-- Notes will be dynamically added here -->
		</div>
	</div>
</div>

<style>
	.page-container {
		display: flex;
		height: 85vh; /* Set the height of the container */

		border: 3px solid black;
		border-radius: 15px;
		min-width: 520px;
	}

	/* Styles API fetched verses */
	#passage-body{
		letter-spacing: 1.0px;
	}

	#bible-passage, #notes-panel {
		width: 50%; /* Divids container into two equal parts */
		height: 100%; /* Fills entire height of the container */
		background-color: #EDEDE9;
		border: 1px solid #ccc; /* Adding a border for visual separation */
		box-sizing: border-box; /* Include borders in width calculation */
		overflow: auto; /* Enable scrolling if content overflows */
		border-radius: 15px;
	}

	#bible-passage{
		border-right: 3px solid #ccc;
		text-align: center;
	}

	#notes-panel{
		border-left: 3px solid #ccc;
		background-color: #D4CCC3;
	}

	#bible-passage header, #notes-panel header{
		height: 50px;
  		width: 100%;
		background-color: #c6cf87;
  		border: 1px solid #c6cf87;
		text-align: center;
	}

	#notes-list{
		margin-left: 10px;
	}
</style>