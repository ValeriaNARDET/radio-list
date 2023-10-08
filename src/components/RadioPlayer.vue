<script setup lang="ts">
import { ref } from 'vue'

interface Track {
    artist: string;
    duration: string;
    iTunesTrackUrl: string;
    imageUrl: string;
    status: string;
    time: string;
    title: string;
}

const playList = ref<Track[]>([]);
const hystoryList = ref<Track[]>([]);
// const playingTime = ref<number>(0);
const playingTime = ref<number>(162);
const progressBarValue = ref<number>(0);
const currentTrack = ref<Track>({
    artist: "",
    duration: "",
    iTunesTrackUrl: "",
    imageUrl: "",
    status: "",
    time: "",
    title: "",
});

const fetchCurrentlyPlaying = () => {
    return fetch('/radio.json')
        .then(response => response.json()
            .then(data => data.nowplaying));
}

const convertToSeconds = (time: string): number => {
    const timeArray = time.split(":")
    const hours = +timeArray[0];
    const minutes = +timeArray[1];
    const seconds = +timeArray[2];
    return hours * 60 + minutes * 60 + seconds;
}

const sortLastListened = (list: Track[]) => {
    return list.sort((a: Track, b: Track) => {
        const prev = new Date(a.time).getTime();
        const next = new Date(b.time).getTime();

        return next - prev;
    })
}

const getDuration = (duration: string) => duration.slice(3);

const getProgressBarValue = (): number => {
    if (currentTrack.value.duration) {
        let time = 100 / convertToSeconds(currentTrack.value.duration) * playingTime.value;
        return Math.round(time);
    } else {
        return 0;
    }
}

const updateTrackList = async () => {
    playList.value = await fetchCurrentlyPlaying();

    if (playList.value && playList.value.length) {
        currentTrack.value = playList.value.find(track => track.status === 'playing');
        convertToSeconds(currentTrack.value.duration);

    }

    hystoryList.value = playList.value.filter(track => track.status === 'history');
    hystoryList.value = sortLastListened(hystoryList.value);
    progressBarValue.value = getProgressBarValue();
}

updateTrackList();
</script>

<template>
    <div class="current-track">
        <div class="current-track-info">
            <div class="controllers">
                <!-- here should be buttons -->
            </div>
            <div id="volume-settings">
                <svg class="chrome-volume__icon" aria-role="presentation" version="1.1" viewBox="0 0 64 64">
                    <path transform="translate(2,11.149)"
                        d="m23.477 39.911c1.4129 0 2.431-1.0389 2.431-2.431v-33.141c0-1.3921-1.0181-2.5349-2.4726-2.5349-1.0181 0-1.7038 0.43634-2.805 1.4752l-9.2046 8.6644c-0.14545 0.12464-0.31166 0.18698-0.51945 0.18698h-6.2126c-2.9297 0-4.5088 1.5999-4.5088 4.7374v8.0411c0 3.1167 1.5791 4.7166 4.5088 4.7166h6.2126c0.20779 0 0.374 0.06234 0.51945 0.18698l9.2046 8.7475c0.99732 0.93501 1.8285 1.3506 2.8466 1.3506z">
                    </path>
                    <path class="chrome-volume__wave chrome-volume__wave-1" transform="translate(2,11.149)"
                        d="m34.864 29.959c0.70647 0.49868 1.7246 0.35323 2.3271-0.47787 1.6205-2.1817 2.5971-5.3815 2.5971-8.6436 0-3.2621-0.9766-6.4411-2.5971-8.6436-0.60255-0.83111-1.5999-0.97655-2.3271-0.49868-0.89345 0.62336-1.0181 1.683-0.35319 2.5765 1.2051 1.6207 1.9323 4.0932 1.9323 6.5658 0 2.4726-0.76881 4.9451-1.9531 6.5866-0.62332 0.89345-0.51945 1.9116 0.374 2.5349z">
                    </path>
                    <path class="chrome-volume__wave chrome-volume__wave-2" transform="translate(2,11.149)"
                        d="m43.154 35.569c0.81021 0.54023 1.8077 0.33245 2.3894-0.49867 2.7426-3.8231 4.3426-8.9137 4.3426-14.233 0-5.3399-1.5583-10.451-4.3426-14.254-0.60255-0.81034-1.5791-1.0181-2.3894-0.47787-0.78979 0.54021-0.91447 1.5583-0.29106 2.4518 2.2647 3.3245 3.6779 7.6878 3.6779 12.28s-1.3923 8.9969-3.6779 12.28c-0.60255 0.89345-0.49872 1.9116 0.29106 2.4518z">
                    </path>
                    <path class="chrome-volume__wave chrome-volume__wave-3 chrome-volume__wave-hidden"
                        transform="translate(2,11.149)"
                        d="m51.527 41.241c0.76894 0.51945 1.7872 0.31166 2.3898-0.54021 3.8438-5.423 6.0255-12.446 6.0255-19.864s-2.2443-14.42-6.0255-19.864c-0.60255-0.87268-1.6209-1.0805-2.3898-0.54021-0.78936 0.56098-0.91404 1.5791-0.31149 2.4518 3.3451 4.9244 5.423 11.241 5.423 17.952 0 6.7113-1.9945 13.132-5.423 17.952-0.60255 0.87268-0.47787 1.8908 0.31149 2.4518z">
                    </path>
                </svg>
                <input type="range" min="0" max="1" step=".01" value="0.5">
            </div>
            <div>
                <h3 class="current-track-title">{{ currentTrack.title }}</h3>
                <h4 class="current-track-artist">{{ currentTrack.artist }}</h4>
            </div>
            <div class="current-track-img">
                <img :src="currentTrack.imageUrl" :alt="currentTrack.title">
            </div>
        </div>
        <div id="progress-bar">
            <div id="progress" :style="`width: ${progressBarValue}%`"></div>
        </div>
    </div>

    <div class="hystory-list" v-if="hystoryList.length">
        <div v-for="(track, index) in hystoryList" :key="track.time" class="track-item">
            <div class="track-number">
                <span>{{ index + 1 }}</span>
            </div>
            <div class="track-img">
                <img :src="track.imageUrl" :alt="track.artist">
            </div>
            <div class="track-info">
                <p class="track-title">
                    <b>{{ track.title }}</b>
                    <span> - {{ track.artist }}</span>
                </p>
                <p>{{ getDuration(track.duration) }}</p>
            </div>
        </div>
    </div>
</template>

<style>
.current-track {
    position: sticky;
    top: 0;
    left: 0;
    right: 0;
    margin-bottom: 40px;
    padding-top: 20px;
    background-color: #242424;
}

.controllers {
    width: 200px;
    height: 80px;
    margin-bottom: 10px;
    border: 1px solid #403e3e;
    border-radius: 5px;
    background: repeating-linear-gradient(135deg, #403e3e, #403e3e 4px, transparent 4px, transparent 10px);
}

#volume-settings {
    height: 100px;
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 0 30px;
}

#volume-settings svg {
    padding: 2px;
    width: 40px;
    height: 40px;
    margin-right: 10px;
    box-sizing: border-box;
    fill: #403e3e;
}

input[type="range"] {
    display: block;
    width: 100%;
    max-width: 100%;
    height: 3px;
    margin: 0px;
    border: 0px;
    color: #e5e5e5;
    appearance: none;
    cursor: pointer;
}

.current-track-info {
    display: flex;
    justify-content: flex-end;
    align-items: flex-end;
}

.current-track-title {
    margin: 5px 0;
    font-size: 36px;
    font-weight: bold;
}

.current-track-artist {
    margin: 10px 0;
    font-size: 22px;
    font-weight: bold;
}

.current-track-img img {
    width: 120px;
    height: 120px;
    margin-left: 30px;
}

#progress-bar {
    min-width: 100%;
    width: 100%;
    height: 10px;
    background: #34495ecc;
    overflow: hidden;
}

#progress {
    width: 0%;
    height: 10px;
    background: #517293;
}

.track-item {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    margin-bottom: 5px;
    border-bottom: 1px dashed #393939;
}

.track-number {
    flex-basis: 40px;
}

.track-img img {
    width: 50px;
    height: 50px;
}

.track-info {
    flex: 1;
    display: flex;
    justify-content: space-between;
    padding: 0 30px;
}

.track-title {
    font-size: 18px;
    text-align: left;
}
</style>