<script>
    export let authKey = undefined

    let kanji = {}
    let kanjiList = []

    let error = false
    let errorStatus = "No error information"

    let reveal = false

    let serifFont = false
    $: characterClass = serifFont ? "kanji serif" : "kanji"

    function randomKanji() {
        let index = Math.floor(Math.random() * kanjiList.length)
        kanji = kanjiList[index]
        console.log(kanji)
        reveal = false
    }
    
    function loadData() {
        fetch(`https://kanjiapi.dev/v1/kanji/grade-${authKey.replace("GRADE","")}`)
        .then(
            res => {
                res.json().then(
                    json => {
                        Promise.all(
                            json.map(async (k) => {
                                let details = await fetch(`https://kanjiapi.dev/v1/kanji/${k}`)
                                let kjson = await details.json()
                                return {
                                    character: k,
                                    readings: {
                                        on: kjson.on_readings,
                                        kun: kjson.kun_readings
                                    },
                                    meanings: kjson.meanings
                                }
                            })
                        ).then((l) => {
                            kanjiList = l
                            randomKanji()
                        }).catch(() => {
                            error = true
                            errorStatus = "Could not parse kanji API response. Please try again later."
                        })
                    }
                )
            }
        ).catch(
            err => {
                error = true
                errorStatus = "Could not contact kanji API. Please try again later."
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
                    {meaning};&MediumSpace;
                {/each}
            </h2>
            {#if kanji.readings.on.length > 0}
            <p>音読み: 
                {#each kanji.readings.on as reading}
                    {reading};&MediumSpace;
                {/each}
            </p>
            {/if}
            {#if kanji.readings.kun.length > 0}
            <p>訓読み:
                {#each kanji.readings.kun as reading}
                    {reading}
                    &MediumSpace;
                {/each}
            </p>
            {/if}
            {#if reveal}
                <span class="{characterClass}">{kanji.character}</span>
            {/if}
        </div>
        <br/>
        <!-- <button>Show radicals</button> -->
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