<script>
  import { onMount } from 'svelte';
  import bgImage from '/src/lib/images/road.png';

  // This is a variable to hold the note from local storage.
  let notesHTML = '';

  onMount(() => {
    bgImage;
    // Function to get all notes from local storage.
    function getAllVerseNotes() {
      // Create an empty object to store all notes.
      const allVerseNotes = {};

      // Iterate over all keys in local storage to retrieve the notes.
      for (let i = 0; i < localStorage.length; i++) {
        const key = localStorage.key(i);

        if (key != null && key.startsWith('verseNotes-')) {
          //@ts-ignore
          const verseNotes = JSON.parse(localStorage.getItem(key));
          /*
          Key.split takes the string (`key`) and splits it into an array
          whenever there is a hyphen("-").

          Ex: "verseNotes-Genesis-1" => ["verseNotes", "Genesis", "1"].

          Apply destructuring by taking the array produced by key.split('-'), 
          and assign its elements to the variables _(throwaway), book, and 
          chapter.
          */
          const [_, book, chapter] = key.split('-');
          /*
          Combine the values book and chapter into a string to act as keys and 
          assign it to verseNotes.
          */
          //@ts-ignore
          allVerseNotes[`${book}-${chapter}`] = verseNotes;
        }
      }

      return allVerseNotes;
    }

    // Get all verse notes from local storage.
    const allVerseNotes = getAllVerseNotes();

    // Flatten the notes for display.
    const notes = Object.values(allVerseNotes).flat();

    // Loop through notes to dynamically generate the HTML.
    notes.forEach((note) => {
      // Concatenate each note's HTML without adding a separator.
      notesHTML += `
        <div class="note">
          <p>${note}</p>
        </div>`;
    });

    // Set the inner HTML of "notes-list" to the generated "notesHTML".
    // @ts-ignore
    document.getElementById('notes-list').innerHTML = notesHTML;
  });
</script>

<main>
  <div class="page-container">
    <img class="page-bg" src="{bgImage}" alt="Image of road surrounded by a vast forest with a cloudy sky" />
    <div id="notes-panel">
      <header>
        <p id="notes-header">Notes</p>
        <p>All notes taken on the Bible page will appear here.</p>
      </header>
      <div id="notes-list">
          <!-- Dynamically generated here -->
      </div>
    </div>
  </div>
</main>

<style>
  @import url('https://fonts.cdnfonts.com/css/varela-round-3');

  .page-container {
    overflow: hidden;
    position: relative;
    width: 100%;
    height: 100vh;
    padding-bottom: 150px;
    background-color: rgb(39, 34, 34, 0.7);
  }

  .page-bg {
    width: 100%;
    height: 100%;
    object-fit: cover;
    z-index: -1;
    position: absolute;
  }

  #notes-panel {
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    width: 100%;
    text-align: center;
    color: var(--gold);
    font-size: 20px;
  }

  #notes-panel header {
    font-family: 'Varela Round', sans-serif;
    font-size: 20px;
    background-color: var(--cadetgrey);
    color: white;
    padding: 10px;
    text-align: center;
  }

  .note {
    margin: 0; /* Remove default margin */
    padding: 10px; /* Add padding for spacing between notes */
  }
</style>