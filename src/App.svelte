<script lang="ts">
    import * as Tone from 'tone'

    const meter = new Tone.Meter()
    const mic = new Tone.UserMedia().connect(meter)

    const handleInit = async () => {
        try {
            await mic.open()
            console.log('mic open')
            const shifter = new Tone.PitchShift(5)
            const reverb = new Tone.Freeverb(0.1)
            const filter = new Tone.Filter(1000, 'lowpass').toDestination()
            mic.connect(shifter)
            shifter.connect(reverb)
            reverb.connect(filter)
        } catch (e) {
            console.log('mic not open')
        }
    }
</script>

<main>
    <button on:click={handleInit}>初期化</button>
</main>

<style lang="scss">
    :root {
        font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto,
            Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    }

    main {
        text-align: center;
        padding: 1em;
        margin: 0 auto;
    }

    .record-btn {
        &[data-color='red'] {
            background-color: red;
        }
        &[data-color='blue'] {
            background-color: blue;
            color: white;
        }
    }
</style>
