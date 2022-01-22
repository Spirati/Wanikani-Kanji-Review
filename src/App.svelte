<script>
  import Login from './Login.svelte'
  import ReviewWindow from './ReviewWindow.svelte'

  let authKey = window.localStorage.getItem('authKey')

  function clearAuth() {
    window.localStorage.removeItem('authKey')
    authKey = undefined
  }
</script>

<svelte:head>
  <title>WaniKani Kanji Review</title>
</svelte:head>

<main>
  {#if authKey}
    <h1>WaniKani Kanji Writing Practice</h1>
    <ReviewWindow {authKey}/>
  {:else}
    <h1>WaniKani Kanji Writing Practice</h1>
    <Login bind:authKey/>
  {/if}
  <br/>
  <button on:click={clearAuth}>Log out</button>
</main>

<style>
  main {
    text-align: center;
  }
</style>
