<script>
    import logo from '/src/lib/images/ff-logo.png'
</script>

<header>
  <div class="navbar">
    <a class="left-corner" href="/">
      <img src="{logo}" alt="FaithFocus Logo" class="logo"> <!-- Added logo image -->
    </a>
  </div>
</header>

<style>
  header {
    padding: 5px;
    margin-left: 10px;
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

  .left-corner img{
      width: 150px;
      height: 50px;
  }
</style>

