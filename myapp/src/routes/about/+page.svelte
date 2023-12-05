<script>
	  import { onMount } from 'svelte';
    import image1 from '/src/lib/images/jason.png';
    import image2 from '/src/lib/images/amy.png';
    import image3 from '/src/lib/images/jae.png';

    onMount(async () => {
		image1;
    image2;
    image3;
	});
</script>

<body>
    <div class="about-section">
    <h1>About Us</h1>
        <p>Welcome to Faith Focus, where we believe that a deeper connection with the Bible can transform lives.</p>

        <p>
            We've crafted an immersive digital space that seamlessly integrates the timeless wisdom of 
            the Bible with modern technology, providing users with a unique and enriching experience.
        </p>
        
        <p>
            Our platform goes beyond the conventional by not only placing the Bible at your fingertips but also by offering a dynamic notes page 
            that empowers you to engage in profound reflection, take meticulous notes, and highlight key passages. We understand that effective Bible 
            study involves more than just reading; it requires interaction, contemplation, and the ability to capture personal insights. 
        </p>
        
        <p>
            Join us on this transformative journey as we strive to make the Bible more accessible and applicable to your everyday life. At Faith Focus, 
            we are not just an app; we are a community united by a shared commitment to deepening our understanding of the sacred teachings that guide us. 
            Let us be your companion on the path to spiritual growth and maturity.
        </p>
    </div>

    <h2 style="text-align:center">Our Team</h2>
    <div class="row">
        <div class="column">
            <div class="card1">
                <img src="{image1}" alt="Image of Jason" style="width:100%">
                <div class="container">
                    <h2>Jason Asonye</h2>
                    <p class="title">Developer</p>
                    <p>Jason.Asonye@gordon.edu</p>
                </div>
            </div>
        </div>

        <div class="column">
            <div class="card2">
                <img src="{image2}" alt="Image of Amy" style="width:100%">
                <div class="container">
                    <h2>Amy Toledo</h2>
                    <p class="title">UI Designer</p>
                    <p>Amy.Toledo@gordon.edu</p>
                </div>
            </div>
        </div>
        
        <div class="column">
            <div class="card3">
                <img src="{image3}" alt="Image of Jae" style="width:100%">
                <div class="container">
                    <h2>Jae Min Lee</h2>
                    <p class="title">Developer</p>
                    <p>Jaemin.Lee@gordon.edu</p>
                </div>
            </div>
        </div>
    </div>
    <footer>
    </footer>
</body>

<style>
    @import url('https://fonts.cdnfonts.com/css/varela-round-3');

    body {
      font-size: 16px;
      font-family: 'Varela Round', sans-serif;
      margin: 0;
      box-sizing: border-box;
      background-color: var(--notesbgcolor);
    }

    footer {
      background-color: var(--hdrcolor);
      position:fixed;
      left: 0;
      bottom: 0;
      width: 100%;
      height: 2.5vh;
      text-align: center;	
      z-index: 1;
	  }
    
    *, *:before, *:after {
      box-sizing: inherit;
    }
    
    .column {
      float: left;
      width: 33.3%;
      margin-bottom: 16px;
      padding: 0 8px;
    }
    
    .card1, .card2, .card3 {
      box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
      margin: 8px 8px 50px 8px;
    }
    
    .about-section {
      padding: 50px;
      text-align: center;
      background-color: #5d6a5b;
      color: white;
    }
    
    .container {
      padding: 0 16px;
    }
    
    .container::after, .row::after {
      content: "";
      clear: both;
      display: table;
    }

    
    @media screen and (max-width: 650px) {
      .column {
        width: 100%;
        display: block;
      }
    }
</style>
