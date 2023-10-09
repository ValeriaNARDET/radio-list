<script setup lang="ts">
import { defineProps } from 'vue';
import Track from "../types/Track";

const { hystoryList } = defineProps<{ hystoryList: Track[] }>();

const getDuration = (duration: string) => duration.slice(3);

</script>

<template>
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

<style scoped>
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