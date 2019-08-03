<template>
    <div class="audio-player">
        <audio :controls="false" ref='audio' @canplay='updateDuration' @timeupdate='updateTime' @ended='onEnded' :src="audioUrl"></audio>

        <div class='player'>

            <div @click='togglePlay' class='play-button'>
                <div class='text-wrapper'>
                    <h4>{{buttonText}}</h4>
                </div>
                <div class="icon-wrapper">
                    <svg x="0px" y="0px" viewBox="0 0 16 15" class='play-icon' v-if='!playing'>
                        <path d="M2.5,1v13l11-6.5L2.5,1L2.5,1z"/>
                    </svg>
                    <svg x="0px" y="0px" viewBox="0 0 357 357" class='pause-icon' v-if='playing'>
                        <path d="M25.5,357h102V0h-102V357z M229.5,0v357h102V0H229.5z"/>
                    </svg>
                </div>
            </div>

            <div ref='progress' class='pregress-wrapper' @mousemove='onHover' @mouseout='hoverOff = true' @click="onClick" :class='{"playing" : currentTime > 0}'>
                <span class='progress-time'>{{currentTime | formatTime}}/{{duration | formatTime}}</span>
                <span class='audio-progress-bar' :style='"width: " + progress + "%;"'></span>
                <span class='handle-wrapper' :class='{"time-right" : mouseX < 9, "hide" : hoverOff}' :style='"left: " + mouseX + "%;"'>
                    <span class="handle"></span>
                    <span class='seek-time'>{{seekTime | formatTime}}</span>
                </span>
                <span class="seek-bg" :class='{"hide" : hoverOff}' :style='"width: " + mouseX + "%;"'></span>
            </div>

        </div>
    </div>
</template>

<script>
export default {
    name: "audio-player",

    data: () => ({
        playing: false,
        currentTime: 0,
        duration: 0,
        mouseX: null,
        hoverOff: true,
        playAllow: true
    }),

    props: ["model", "audioUrl"],

    mixins: [],

    watch: {
    },

    mounted() {
    },

    filters: {
        formatTime(value) {
            let duration = Math.ceil(value)

            let hours = Math.floor(duration / 3600)
            duration %= 3600;
            let minutes = Math.floor(duration / 60);
            minutes < 10 ? minutes = "0" + minutes : false
            let seconds = duration % 60;
            seconds < 10 ? seconds = "0" + seconds : false

            return hours == 0 ? (minutes + ":" + seconds) : (hours + ":" + minutes + ":" + seconds)
        }
    },

    methods: {
        togglePlay() {
            if (!this.playing && this.playAllow) {
                this.$refs.audio.play()
                this.playing = true
            } else {
                this.$refs.audio.pause()
                this.playing = false
            }
        },

        updateTime(data) {
            this.currentTime = data.target.currentTime
        },

        updateDuration(data) {
            this.duration = data.target.duration
        },

        onEnded() {
            this.playing = false
            this.$refs.audio.currentTime = 0
        },

        onClick(e) {
            this.$refs.audio.currentTime = (e.layerX / e.srcElement.clientWidth) * this.duration
        },

        onHover(e) {
            this.hoverOff = false
            this.mouseX = (e.layerX / e.srcElement.clientWidth) * 100
        }
    },

    computed: {
        buttonText() {
            return this.playing ? 'Pause' : 'Play'
        },

        progress() {
            return (this.currentTime/this.duration) * 100
        },

        seekTime() {
            return this.duration * this.mouseX / 100
        }
    },

    components: {
    }
};
</script>

<style scoped="">
    .audio-player {
        width: 100%;
    }

    .player {
        display: flex;
        width: 100%;
    }

    .play-button {
        padding: .5rem 2.5rem .5rem 2rem;
        background-color: #0f0487;
        color: white;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
    }

    .icon-wrapper {
        border: 1px solid currentColor;
        border-radius: 50%;
        width: 2rem;
        height: 2rem;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .text-wrapper {
        width: 3.5em;
        margin-right: 1rem;
        display: flex;
        justify-content: flex-end;
        text-transform: uppercase;
    }

    .play-icon,
    .pause-icon {
        width: 0.75rem;
        height: 0.75rem;
        fill: currentColor;
        cursor: pointer;
    }

    .play-icon {
        margin-left: 0.15rem;
    }

    .pregress-wrapper {
        transform-origin: left;
        transform: scaleX(0);
        transition: .4s ease-out;
        background-color: white;
        color: black;
        pointer-events: none !important;
        display: flex;
        align-items: center;
        justify-content: center;
        flex-grow: 1;
        position: relative;
    }

    .pregress-wrapper.playing {
        transform: scaleX(1);
        pointer-events: auto !important;
    }

    .audio-progress-bar {
        position: absolute;
        top: 0;
        left: 0;
        height: 100%;
        background-color: #f0cfa1;
        pointer-events: none;
    }

    .handle-wrapper {
        position: absolute;
        top: 0;
        width: 2px;
        height: 100%;
        z-index: 10;
        transform: translateZ(0) translateX(-100%);
        pointer-events: none !important;
        transition: opacity .4s;
    }

    .handle-wrapper.time-right .seek-time {
        left: 100%;
        margin-left: .25rem;
        margin-right: 0;
    }

    .handle-wrapper .handle {
        background-color: black;
        width: 100%;
        height: 100%;
        display: block;
    }

    .progress-time {
        position: relative;
        z-index: 100;
        pointer-events: none !important;
        color: black;
    }

    .seek-time {
        position: absolute;
        top: 0;
        right: 100%;
        margin-right: .25rem;
        margin-top: .25rem;
        color: black;
    }

    .seek-bg {
        position: absolute;
        top: 0;
        left: 0;
        height: 100%;
        background-color: #f0cfa1;
        opacity: .3;
        pointer-events: none !important;
        z-index: -1;
        transition: opacity .4s;
    }

    .seek-bg.hide, .handle-wrapper.hide {
        opacity: 0;
    }
</style>
