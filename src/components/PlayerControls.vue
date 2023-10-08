<template>
    <div class="player-controls">
        <button name="play-pause" class="button play-pause-button" aria-label="Play/pause">
            <i class="fas fa-play" aria-hidden></i>
        </button>

        <div class="volume-and-title">
            <div class="currently-playing" aria-label="Currently playing">
                <span class="currently-playing-label">Now playing on Some Radio Station</span>
                <span class="currently-playing-title">Listen to Some Radio Station</span>
            </div>

            <div class="volume-controls">
                <button name="mute" class="button mute-button" aria-label="Mute/unmute">
                    <i class="fas fa-volume-down" aria-hidden></i>
                </button>

                <input type="range" name="volume" class="volume" min="0" max="1" step="0.05" value="0.2"
                    aria-label="Volume">
            </div>
        </div>
    </div>
</template>

<style scoped>
.button {
    vertical-align: middle;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    border: none;
    background-color: #F59E0B;
    color: #fff;
    border-radius: 100%;
}

.play-pause-button {
    width: 70px;
    height: 70px;
    font-size: 25px;
    margin-right: 24px;
}

.mute-button {
    width: 30px;
    height: 30px;
    margin-right: 12px;
}

.currently-playing-label {
    font-size: 12px;
    font-weight: 300;
}

.currently-playing-title {
    font-size: 22px;
}

.volume {
    -webkit-appearance: none;
    appearance: none;
    border: 1px solid #000;
    border-radius: 50px;
    overflow: hidden; /* This will help with styling the thumb */
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;

  height: 15px;
  width: 15px;

  cursor: ew-resize;
  background: #F59E0B;
  box-shadow: -400px 0 0 390px #FDE68A;
  border-radius: 50%;
}

input[type="range"]:focus {
  border-radius: 50px;
  box-shadow: 0 0 15px -4px #F59E0B;
}
</style>