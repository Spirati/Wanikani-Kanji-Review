<script>
import { fade } from 'svelte/transition'
export let closeFunction
</script>

<div class="container" transition:fade="{{duration: 50}}">
    <div class="infobox">
        <h2>What is this?</h2> <button on:click={closeFunction}>Close</button>
        <br/>
        <p>Once you log in or select a grade level, you'll see a box with the following information:</p>
        <ul>
            <li>The <b>meanings</b> of a kanji</li>
            <li>The <b>kun/on readings</b> of a kanji</li>
        </ul>
        <p>Your job is to try to <b>recall the kanji and write it by hand</b> on, say, a piece of paper or in a drawing application.</p>
        <p>Once you feel like you have a good guess or you give up, click the <b>"Reveal answer"</b> button to see the kanji. Try to rewrite it a couple times to let it really sink in!</p>
        <p>You can toggle the font in which the character is displayed using the checkbox on the bottom - it's good to get used to seeing characters in multiple different typefaces.</p>

    </div>
</div>

<style>
    .container {
        position: fixed;
        width: 100%;
        height: 100%;
        text-align: center;
        align-self: center;
    }
    .infobox {
        background-color: white;
        border: 2px solid black;
        margin: auto;
        width: 50%;
    }
    .infobox h2 {
        display: inline;
        padding-bottom: 0;
    }
    ul {
        list-style-type: none;
    }
    p {
        padding-left: 10%;
        padding-right: 10%;
    }
</style>