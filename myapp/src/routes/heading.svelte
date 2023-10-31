<!-- <script>
	import logo from '$lib/images/ff-logo.png'
</script>

<header>
	<div class="navbar">
		<a class = "unstyled text-xl" href="/">
			<b> FaithFocus 🔍</b>
		</a>
	</div>
</header>


<style>
	header{
		background-color: #DDDBCB;
	}

	.navbar{
		margin-top: 20px;
	}

	.navbar b{
		width: 50px;
		height: 50px;
		margin-left: 20px;
		font-size: 30px;
	}


	
</style> -->

<script>
    import logo from '/Users/jasonye19/cps353/cps353-2023-joint-lee-asonye-toledo/myapp/src/lib/images/ff-logo.png'
</script>

<header>
  <div class="navbar">
    <a class="stump" href="/">
      <img src="{logo}" alt="FaithFocus Logo" class="logo"> <!-- Added logo image -->
    </a>
  </div>
</header>

<style>

  header {
    background-color: #DDDBCB;
    padding: 10px; /* Added padding for better visibility */
  }

  .navbar {
    display: flex; /* Display the elements in a row */
    align-items: center; /* Align items vertically in the center */
  }

  .logo {
    width: 50px; /* Set the width of the logo */
    height: 50px; /* Set the height of the logo */
    margin-right: 10px; /* Adjust margin for proper positioning */
  }

  .navbar b {
    font-size: 30px;
  }

  .stump img{
      width: 200px;
      height: 150px;
  }
</style>

