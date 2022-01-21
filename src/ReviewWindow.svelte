<script>
    export let authKey = undefined

    let kanji = {}
    let kanjiList = []
    let userData = {}

    let error = false
    let errorStatus = "No error information"

    let reveal = false

    let serifFont = false
    $: characterClass = serifFont ? "kanji serif" : "kanji"

    function randomKanji() {
        let index = Math.floor(Math.random() * kanjiList.length)
        console.log(kanjiList[index].data)
        let readings = kanjiList[index].data.readings
        kanji = {
            character: kanjiList[index].data.slug,
            readings: {
                on: readings.filter(r => r.type == "onyomi"),
                kun: readings.filter(r => r.type == "kunyomi")
            },
            meanings: kanjiList[index].data.meanings
        }
        console.log(kanji)
        reveal = false
    }
    
    function loadData() {
        fetch(`https://api.wanikani.com/v2/user`, {
            headers: {
                Authorization: `Bearer ${authKey}`
            }
        }).then(
            res => {
                res.json().then(
                    json => {
                        userData = json
                        console.log(userData)
                        loadKanji()
                    }
                )
            }
        ).catch(
            err => {

            }
        )
    }

    function loadKanji() {
        let levels = new Array(userData.data.level).fill(0).map((_, i) => i+1)
        fetch(`https://api.wanikani.com/v2/subjects?types=kanji&levels=${levels.join(",")}`, {
            headers: {
                Authorization: `Bearer ${authKey}`
            }
        }).then(
            (result) => {
                if (result.status == 401 || result.status == 404) {
                    error = true
                    errorStatus = "Could not load kanji. Please log out and log back in."
                    return
                }
                result.json().then(
                    json => {
                        kanjiList = json.data
                        randomKanji()
                    }
                ).catch(
                    err => {
                        error = true
                        console.log(err)
                        errorStatus = "Could not parse WaniKani kanji output. Please log out and log back in."
                    }
                )
            }
        ).catch(
            (err) => {
                error = true
                errorStatus = "Could not contact the WaniKani servers. Please try again later."
            }
        )
    }

    loadData()
</script>

{#if error}
    <h2 class="error">{errorStatus}</h2>
{:else}
    {#if kanjiList.length > 0}
        <div class="window">
            <h2>
                {#each kanji.meanings as meaning}
                    {#if meaning.primary}
                        <u>{meaning.meaning}</u>
                    {:else}
                        {meaning.meaning}
                    {/if}
                    &MediumSpace;
                {/each}
            </h2>
            {#if kanji.readings.on.length > 0}
            <p>音読み: 
                {#each kanji.readings.on as reading}
                    {#if reading.primary}
                        <u>{reading.reading}</u>
                    {:else}
                        {reading.reading}
                    {/if}
                    &MediumSpace;
                {/each}
            </p>
            {/if}
            {#if kanji.readings.kun.length > 0}
            <p>訓読み:
                {#each kanji.readings.kun as reading}
                    {#if reading.primary}
                        <u>{reading.reading}</u>
                    {:else}
                        {reading.reading}
                    {/if}
                    &MediumSpace;
                {/each}
            </p>
            {/if}
            {#if reveal}
                <span class="{characterClass}">{kanji.character}</span>
            {/if}
        </div>
        <br/>
        <button>Show radicals</button>
        <button on:click={() => reveal = true}>Reveal answer</button>
        <button on:click={randomKanji}>New kanji</button>
        <label for="serif">Serif font: </label>
        <input type="checkbox" bind:checked={serifFont} id="serif" name="serif">
    {:else}
        <h2>Loading kanji...</h2>
    {/if}
{/if}

<style>
    .window {
        margin: auto;
        padding: 1em;
        width: 50%;
        border-radius: 1.5em;

        text-align: center;
        background-color: lightblue;

        font-family: Osaka, Arial, sans-serif;
    }
    p {
        font-size: 2em;
        margin: 0;
        padding: 0;
    }
    .kanji {
        font-size: 15em;
    }
    .serif {
        font-family: 'ヒラギノ明朝 ProN' , 'Hiragino Mincho ProN' , '游明朝','游明朝体',YuMincho,'Yu Mincho' , 'ＭＳ 明朝' , 'MS Mincho' , HiraMinProN-W3 , 'TakaoEx明朝' , TakaoExMincho , 'MotoyaLCedar' , 'Droid Sans Japanese' , serif;
    }
    .error {
        color: red;
    }

    label {
        display: inline;
    }
</style>