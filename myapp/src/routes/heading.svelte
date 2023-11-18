<script>
  import logo from '/src/lib/images/ff-logo.png';

  // Menu for mobile devices
  let showMenu = false;

  // Not sure yet
  function onMenuClick() {
    showMenu = !showMenu;
  }
</script>

<header>
  <div class="navbar">
    <a href="/" class="left-corner">
      <img src="{logo}" alt="FaithFocus Logo" class="logo">
    </a>

    <a id="menu-icon" class="menu-icon" on:click={onMenuClick}>
      <!-- Menu -->
      <div class="bar"></div>
      <div class="bar"></div>
      <div class="bar"></div>
    </a>

    <div id="navbar-right" class:visible={showMenu}>
      <a href="/">Home</a>
      <a href="/bible?book=Genesis&chapter=Chapter 1">Bible</a>
      <a href="">About</a>
      <a href="">Contact</a>
    </div>
  </div>
</header>

<style>
  @import url('https://fonts.cdnfonts.com/css/varela-round-3');

  .bar {
    width: 35px;
    height: 5px;
    background-color: white;
    border-radius: 8px;
    margin: 6px 0;
  }

  header {
    padding: 5px;
    background-color: var(--hdrcolor);
    overflow: hidden;
    text-align: center;
  }

  header a {
    color: white;
    float: right;
    text-decoration: none;
    padding: 5px 20px;
    border-radius: 4px;
    font-size: 20px;
    font-family: 'Varela Round', sans-serif;
  }

  header a:hover {
    background-color: var(--bgcolor);
  }

  .navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    white-space: nowrap;
  }

  #navbar-right {
    display: flex;
    flex-direction: row;
  }

  #navbar-right.visible {
    display: flex;
    flex-direction: column;
    margin-left: 50px;
  }

  .logo {
    width: 150px;
    height: 50px;
    margin-right: 10px;
  }

  .left-corner img {
    width: 150px;
    height: 50px;
  }

  .menu-icon {
    display: none;
  }

  @media (max-width: 730px) {

    header {
      padding: 1px;
    }
    #navbar-right {
      display: none;
    }

    #navbar-right.visible {
      display: flex;
      flex-direction: column;
      margin-left: 0;
    }

    .menu-icon {
      display: block;
    }

    .navbar {
      flex-direction: column;
    }

    .logo {
      width: 150px;
      height: 50px;
      margin-right: 10px;
    }

    .left-corner img {
      width: 150px;
      height: 50px;
    }
  }
</style>
