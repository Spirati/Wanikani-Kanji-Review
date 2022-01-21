<script>
    export let authKey = undefined
    let _authKey
    let invalidKey = false

    function setAuthCookie(e) {
        fetch("https://api.wanikani.com/v2/user", {
            headers: {
                "Authorization": `Bearer ${_authKey}`
            }
        }).then(
            (e) => {
                if (e.status == 401) {
                    invalidKey = true
                    _authKey = ""
                } else {
                    window.localStorage.setItem("authKey", _authKey)
                    authKey = _authKey
                }
            }
        ).catch(
            (e) => {
                invalidKey = true
                _authKey = ""
                console.log(e)
            }
        )
    }
</script>

{#if invalidKey}
<p class="warning">Invalid auth key supplied.</p>
{/if}
<form on:submit|preventDefault={setAuthCookie}>
    <label for="auth">WaniKani Auth Key</label>&MediumSpace;<sup><a href="https://www.wanikani.com/settings/personal_access_tokens">(How to get?)</a></sup>
    <br/>
    <input required id="auth" name="auth" bind:value={_authKey}>
    <br/>
    <button type="submit">Submit</button>
</form>

<style>
    label {
        display: inline;
    }
    a:before {
        content: " ";
        text-decoration: none;
    }
    a {
        text-decoration: underline dotted;
    }
    .warning {
        color: red;
    }
</style>