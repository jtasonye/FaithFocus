<script>
	import { onMount } from 'svelte';

	let selectedBook = " ";
	// console.log("BP Selected book: ", selectedBook);
    let selectedChapter = " ";
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
						<sup>${index + 1}</sup> ${verse.text} </span>`;
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

	
	function updateNotesPanel() {
		const notesBody = document.getElementById("notes-body");

		if (notesBody !== null) {
			// Initialize an empty string to store the HTML content
			let notesHTML = "";

			// Loop through verseNotes to generate the HTML for notes
			verseNotes.forEach(notes => {
				notes.forEach(note => {
					// Concatenate each note's HTML without adding a separator
					notesHTML += `<p>${note}</p>`;
				});
			});
			// Set the inner HTML of notesBody to the generated notesHTML
			notesBody.innerHTML = notesHTML;
		}
	}

</script>

<div class="page-container">

	<div id="bible-passage">
		<header>
			<p id="passage-header"> This is temp header</p>
		</header>

		<!-- @html ignores inline css styling and outputs variable -->
		<p> {@html biblePassage}</p>
	</div>

	<div id="notes-panel">
		<header>
			<p id="notes-header"> Your Notes - click on a verse to get started </p>
		</header>

		<div id="notes-body">
			<!-- Notes will be dynamically added here -->
		</div>
	</div>
</div>

<style>
	.page-container {
		display: flex;
		height: 100vh; /* Set the height of the container to the full viewport height */

		border: 3px solid black;
		border-radius: 15px;
		min-width: 520px;
	}

	#bible-passage, #notes-panel {
		width: 50%; /* Divide the container into two equal parts */
		height: 100%; /* Fill the entire height of the container */
		background-color: #EDEDE9;
		border: 1px solid #ccc; /* Adding a border for visual separation */
		box-sizing: border-box; /* Include borders in width calculation */
		overflow: auto; /* Enable scrolling if content overflows */
		border-radius: 15px;
	}

	#bible-passage{
		border-right: 3px solid #ccc;
	}

	#notes-panel{
		border-left: 3px solid #ccc;
	}

	#bible-passage header, #notes-panel header{
		height: 50px;
  		width: 100%;
		background-color: blanchedalmond;
  		border: 1px solid blanchedalmond;
		text-align: center;
	}

	#notes-body{
		margin-left: 10px;
	}
</style>
