<script lang="ts">
    import * as Tone from 'tone'

    const synth = new Tone.Synth().toDestination()

    const handlePlay = () => {
        const now = Tone.now()
        console.log(now)
        synth.triggerAttackRelease('C4', '8n', now)
        synth.triggerAttackRelease('E4', '8n', now + 0.5)
        synth.triggerAttackRelease('G4', '8n', now + 1)
    }

    setInterval(() => console.log(Tone.now()), 1000)

    let mediaRec: MediaRecorder | null = null
    let chunks: BlobPart[] = []
    let audioWrapper = null

    const handleInit = async () => {
        try {
            const stream = await navigator.mediaDevices.getUserMedia({
                audio: true,
            })

            mediaRec = new MediaRecorder(stream)
            mediaRec.ondataavailable = e => {
                chunks.push(e.data)
            }
            mediaRec.onstop = e => {
                console.log('recorder stopped')
                const audio = document.createElement('audio')
                const blob = new Blob(chunks, {
                    type: 'audio/ogg; codecs=opus',
                })
                chunks = []
                audio.src = URL.createObjectURL(blob)
                audio.setAttribute('controls', '')
                audioWrapper.appendChild(audio)
            }
        } catch (e) {
            console.error(e)
        }
    }

    let recordButtonColor = 'blue'

    const handleRecord = () => {
        mediaRec.start()
        console.log(mediaRec.state)
        console.log('recorder started')
        recordButtonColor = 'red'
    }

    const handleStop = () => {
        mediaRec.stop()
        recordButtonColor = 'blue'
    }
</script>

<main>
    <button on:click={handlePlay}>再生</button>
    <button on:click={handleInit}>初期化</button>
    <button
        class="record-btn"
        on:click={handleRecord}
        data-color={recordButtonColor}>録音</button
    >
    <button class="stop-btn" on:click={handleStop}>停止</button>
    <div bind:this={audioWrapper} />
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
