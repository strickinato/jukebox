<script>
    import { onMount, onDestroy } from "svelte";
    import { Howl } from "howler";

    let playlist = [
        {
            title: "Coming Up Posie",
            src: "/songs/coming-up-posie.m4a",
            duration: "2:33",
        },
        {
            title: "Turtlehead Poo",
            src: "/songs/turtlehead-poo.mp3",
            duration: "2:33",
        },
        {
            title: "Due Date",
            src: "/songs/due-date-song.mp3",
            duration: "1:34",
        },
        {
            title: "Don't Leave Me",
            src: "/songs/dont-leave-me.mp3",
            duration: "1:49",
        },
        {
            title: "Peppero Day",
            src: "/songs/peppero-day.mp3",
            duration: "4:14",
        },
        {
            title: "Take Me Back To Hawaii",
            src: "/songs/take-me-back-to-hawaii.m4a",
            duration: "1:43",
        },
    ];

    let currentSongIndex = 0;
    let isPlaying = false;
    let sound;

    function loadCurrentSong() {
        sound = new Howl({
            src: [playlist[currentSongIndex].src],
            html5: true,
            volume: 0.5,
            onend: playNextSong,
        });
    }
    onMount(loadCurrentSong);
    onDestroy(() => {
        sound?.unload();
    });

    function play() {
        sound?.play();
        isPlaying = true;
    }
    function playAt(index) {
        return () => {
            stop();
            currentSongIndex = index;
            loadCurrentSong();
            play();
            isPlaying = true;
        };
    }

    function stop() {
        sound?.stop();
        isPlaying = false;
    }

    function pause() {
        sound?.pause();
        isPlaying = false;
    }

    function playNextSong() {
        const shouldPlay = isPlaying;
        stop();
        currentSongIndex = (currentSongIndex + 1) % playlist.length;
        loadCurrentSong();
        if (shouldPlay) {
            play();
        }
    }
    function playPreviousSong() {
        const shouldPlay = isPlaying;
        if (sound?.seek() < 2) {
            stop();
            currentSongIndex =
                (currentSongIndex - 1 + playlist.length) % playlist.length;
            loadCurrentSong();
            if (shouldPlay) {
                play();
            }
        } else {
            if (shouldPlay) {
                stop();
                play();
            }
        }
    }
</script>

<div class="wrapper">
    <div class="now-playing">
        <h3>{playlist[currentSongIndex].title}</h3>
    </div>

    <div class="controls">
        <button id="previous-button" on:click={playPreviousSong}>&lt;</button>
        {#if isPlaying}
            <button id="pause-button" on:click={pause}>| |</button>
        {:else}
            <button id="play-button" on:click={play}>|&gt;</button>
        {/if}
        <button id="next-button" on:click={playNextSong}>&gt;</button>
    </div>

    <div class="song-list">
        {#each playlist as { title }, index}
            <button
                class="song"
                class:playing={index === currentSongIndex}
                on:click={playAt(index)}
            >
                {title}
            </button>
        {/each}
    </div>
</div>

<style>
    .wrapper {
        display: flex;
        flex-direction: column;
        gap: 16px;
        height: 100%;
    }

    .now-playing {
        aspect-ratio: 1 / 1;
        margin: 0 auto;
        max-height: 800px;
        min-height: 300px;
        border: 1px solid #1ed760;
        padding: 16px;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .song-list {
        display: flex;
        flex-direction: column;
        padding: 8px;
        border-radius: 8px;
        overflow: scroll;
        background-color: var(--background-base);
        border-radius: 8px;
        padding: 16px;
    }
    button.song {
        display: flex;
        color: #fff;
        background: none;
        border: none;
        font: inherit;
        cursor: pointer;
        font-weight: 500;
        padding: 16px 0;
    }
    .song.playing {
        color: var(--text-active);
    }
    .song:hover {
        font-weight: 800;
    }

    .controls {
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 16px;
        background-color: var(--background-base);
        border-radius: 8px;
        padding: 16px;
    }
    .controls button {
        background-color: #1ed760;
        color: black;
        font-weight: 900;
        border-radius: 50%;
        padding: 8px;
        text-align: center;
        height: 44px;
        width: 44px;
        cursor: pointer;
        border: none;
    }
    .controls button:hover {
        font-size: 18px;
        font-weight: 900;
    }
    .controls #play-button,
    .controls #pause-button {
        height: 60px;
        width: 60px;
    }
</style>
