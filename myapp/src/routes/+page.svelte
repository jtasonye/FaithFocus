<script>
    /** JSDoc comments used to provide type hints for the variable
     * `books`
     * @type {Object.<string, number>}
     */
     let books = {
        // Old Testament
        "Genesis": 50, 
        "Exodus": 40, 
        "Leviticus": 27, 
        "Numbers": 36,
        "Deuteronomy": 34,
        "Joshua": 24,
        "Judges": 21,
        "Ruth": 4,
        "1 Samuel": 31,
        "2 Samuel": 24,
        "1 Kings" : 22,
        "2 Kings" : 25,
        "1 Chronicles" : 29,
        "2 Chronicles" : 36,
        "Ezra" : 10,
        "Nehemiah" : 13,
        "Esther" : 10,
        "Job" : 42,
        "Psalms": 150,
        "Proverbs": 31,
        "Ecclesiastes" : 12,
        "Song of Songs": 8,
        "Isaiah": 66,
        "Jeremiah" : 52,
        "Lamentations" : 5,
        "Ezekiel" : 48,
        "Daniel" : 12,
        "Hosea" : 14,
        "Joel" : 3,
        "Amos" : 9,
        "Obadiah" : 1,
        "Jonah" : 4,
        "Micah" : 7,
        "Nahum" : 3,
        "Habakkuk" : 3,
        "Zephaniah" : 3,
        "Haggai" : 2,
        "Zechariah" : 14,
        "Malachi" : 4,
    
        // New Testament
        "Matthew" : 28,
        "Mark" : 16,
        "Luke" : 24,
        "John" : 21,
        "Acts" : 28,
        "Romans" : 16,
        "1 Corinthians" : 16,
        "2 Corinthians" : 16,
        "Galatians" : 6,
        "Ephesians" : 6,
        "Philippians" : 4,
        "Colossians" : 4,
        "1 Thessalonians" : 5,
        "2 Thessalonians" : 3,
        "1 Timothy" : 6,
        "2 Timothy" : 4,
        "Titus" : 3,
        "Philemon" : 1,
        "Hebrews" : 13,
        "James" : 5,
        "1 Peter" : 5,
        "2 Peter" : 3,
        "1 John" : 5,
        "2 John" : 1,
        "3 John" : 1,
        "Jude" : 1,
        "Revalation" : 22
    };
    
    /** JSDoc comments used to provide type hints for the variable
     * `selectedBook`
     * @type {string}
     */
    let selectedBook = '';
    
    /** JSDoc comments used to provide type hints for the variable
     * `chapters`.
     * @type {string[]}
     */
    let chapters = [];


    // Loads the chapter value according to the book
    function populateChapters() {
        chapters = [];
        const selectedBookChapters = books[selectedBook];
        if (selectedBookChapters) {
            for (let i = 1; i <= selectedBookChapters; i++) {
                chapters.push(`Chapter ${i}`);
            }
        }
    }

    // function to choose between Alphabetical & Traditional
    function chooseOrder(){
        const traditional = document.getElementById("trad");
        let sortedBooks = {col: "name", asceding: true};
    }


    </script>
    
    <main class="m-class">
        <h1>Welcome To FaithFocus</h1>
        <h3>Begin Studying The Word Of God By Taking Notes</h3>
    
        <div class="select-book-chapter">
            <div class="title">
                <p> BEGIN READING</p>
            </div>

            <div class="align">

                <div class="bible-order">
                    <label><input id="trad" type="radio" value="Traditional" name="radio-button"/>Trad.</label>
                    <label><input id="alph" type="radio" value="Alphabetical" name="radio-button"/>Alph.</label>
                </div>
    
                <div class="all-books">
                    <select name="books" bind:value={selectedBook} on:change={populateChapters}>
                        <option value="" disabled>Select a book</option>
                        {#each Object.keys(books) as book}
                            <option value={book}>{book}</option>
                        {/each}
                    </select>
                </div>
    
                <div class="chapter-option">
                    <select name="chapters">
                        <option value="" disabled selected>Select a chapter</option>
                        {#each chapters as chapter}
                            <option value={chapter}>{chapter}</option>
                        {/each}
                    </select>
                </div>
            <!-- align divide -->
            </div>

            <div class = "search-button">
                <a href="/bible">
                    <button type="button">Search Button</button>
                </a>
            </div>

        <!-- select-book-chapter divide-->
        </div>
     </main>
    
   
    
    <style>
        h1, h3, .select-book-chapter {
            text-align: center;
        } 
         .select-book-chapter {
            margin-top: 50px;
            background-color: grey;
            height: 400px;
        }

        /* Aligns .bible-order and .layout-option */
        .align{
            display: flex; /* Added flex to align items horizontally */
            align-items: center; /* Center items vertically */
            justify-content: center; /* Center items horizontally */
        }

        .bible-order input[type="radio"] {
            display: block; /* Display radio buttons on top of each other */
            margin-top: 12px; /* Adds space between buttons */
        }

        label{
            margin-right: 20px;
            line-height: 30px;
        }

        /* This is the `Begin Reading` section */
        .title p {
            font-size: 20px;
            padding-top: 20px;
        }


        select{
            height: 50px;
            width: 200px;
            margin: 10px;
        }

        .search-button button {
            color: black;
            background-color: white;
            border: 1px solid white;
            border-radius: 10px;
            height: 30px;
            width: 300px;
            margin-top: 50px;
        }
    
    </style>

    