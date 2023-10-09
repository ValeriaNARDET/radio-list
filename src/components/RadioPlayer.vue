<script setup lang="ts">
import { ref } from 'vue'
import Track from "../types/Track";
import CurrentTrack from "./CurrentTrack.vue";
import PlayList from "./PlayList.vue";

const playList = ref<Track[]>([]);
const hystoryList = ref<Track[]>([]);
// const playingTime = ref<number>(0);
const playingTime = ref<number>(162); // toDelete (it's for UI example)
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
    <CurrentTrack :current-track="currentTrack" :progress-bar-value="progressBarValue" />

    <div v-if="hystoryList.length">
        <PlayList :hystory-list="hystoryList" />
    </div>
</template>
