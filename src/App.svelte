<script>
  import Login from './Login.svelte'
  import ReviewWindow from './ReviewWindow.svelte'
  import ReviewWindowKyouiku from './ReviewWindowKyouiku.svelte'

  let authKey = window.localStorage.getItem('authKey')
  let gradeLevel

  function clearAuth() {
    window.localStorage.removeItem('authKey')
    authKey = undefined
  }
</script>

<main>
  {#if (authKey || "").substring(0, 5) == "GRADE"}
    <h1>Practicing from Kyouiku grade {authKey.replace("GRADE","")}</h1>
    <ReviewWindowKyouiku {authKey}/>
    <button on:click={clearAuth}>Log out</button>
  {:else if authKey}
    <h1>WaniKani Kanji Writing Practice</h1>
    <ReviewWindow {authKey}/>
    <button on:click={clearAuth}>Log out</button>
  {:else}
    <h1>WaniKani Kanji Writing Practice</h1>
    <Login bind:authKey/>
    <h2>Or, review from Kyouiku kanji without logging in:</h2>
    <select bind:value={gradeLevel}>
      <option value="1">Grade 1</option>
      <option value="2">Grade 2</option>
      <option value="3">Grade 3</option>
      <option value="4">Grade 4</option>
      <option value="5">Grade 5</option>
      <option value="6">Grade 6</option>
      <option value="8">Grade 8</option>
    </select>
    <button on:click={() => {
      authKey = `GRADE${gradeLevel}`
      window.localStorage.setItem("authKey", authKey)
    }}>Go</button>
  {/if}
  <br/>
</main>

<style>
  main {
    text-align: center;
  }
</style>
