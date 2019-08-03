<template>
    <div id="app">
        <div class='player-wrapper'>
            <audio-player audioUrl='/dummy-audio.mp3'></audio-player>
        </div>
    </div>
</template>

<script>
import AudioPlayer from './components/AudioPlayer.vue'

export default {
    name: 'app',
    components: {
        AudioPlayer
    }
}
</script>

<style>
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.player-wrapper {
    width: 50%;
}
</style>