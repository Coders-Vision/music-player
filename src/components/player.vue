<template>
  <div class="player-interface">
    <div class="album-cover">
      <img :src="trackDetails.coverArt" alt="album art" />
    </div>

    <div class="wrapper">
      <div class="track-info">
        <h3 class="title">{{ trackDetails.title || `Unknown Title` }}</h3>
        <h5 class="artist">{{ trackDetails.artist || `Unknown Artist` }}</h5>
        <h5 class="album">{{ trackDetails.album || `Unknown Album` }}</h5>
      </div>
      <div class="player-control">
        <div class="progress-bar">
          <input
            type="range"
            min="1"
            max="100"
             v-on:change="adjustProgressSlider"
            v-model="progressSlider"
            class="seekbar"
          />
          <span class="current-seconds">{{ currentSeconds }}/</span>
          <span class="final-seconds">{{ songDuration }}</span>
        </div>

        <div class="main-btn">
          <div class="btn back">
            <i
              class="fa fa-step-backward"
              @click="skipBackward()"
              aria-hidden="true"
            ></i>
          </div>
          <div class="btn play">
            <i
              :class="[songPlaying ? 'fa fa-pause' : 'fa fa-play']"
              v-on:click="playOrPause()"
              aria-hidden="true"
            ></i>
          </div>
          <div class="btn for">
            <i
              class="fa fa-step-forward"
              @click="skipForward()"
              aria-hidden="true"
            ></i>
          </div>
          <div class="volume-control">
            <span class="vol-icon">
              <i
                :class="[isMuted ? 'fa fa-volume-off' : 'fa fa-volume-up']"
                @click="muteAudio()"
                aria-hidden="true"
              ></i>
            </span>
            <input
              type="range"
              min="0"
              max="1"
              step="0.05"
              v-on:change="adjustVolume"
              v-model="volumeSlider"
              class="vol-slider"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "player",
  data() {
    return {
      player: new Audio(),
      trackDetails: {
        title: "Track Name",
        artist: "Artist Name",
        album: "Album Artist",
        coverArt: require("@/assets/art.png"),
      },
      songPlaying: false,
      progressSlider: 0,
      currentSeconds: "0:00",
      songDuration: "0:00",
      volumeSlider: 0.5,
      isMuted: false,
      songCounter: 0,
    };
  },
  props: {
    playlist: Array,
    selectedSong: Number,
  },
  methods: {
    playAll() {
      this.player.src = this.playlist[this.songCounter].path;
      let playPromise = this.player.play();
      this.songPlaying = true;
      if (playPromise !== undefined) {
        playPromise
          .then(() => {
            this.setTag(this.songCounter);
            this.updateProgressBar();
            this.onEndSong();
          })
          .catch((error) => {
            console.error(error);
          });
      }
    },
    onEndSong() {
      this.player.addEventListener("ended", () => {
        this.songCounter++;
        this.playSelectedSong(this.songCounter);
      });
    },
    playSelectedSong(songNumber) {
      this.player.src = this.playlist[songNumber].path;
      let playPromise = this.player.play();
      if (playPromise !== undefined) {
        playPromise.then(() => {
          this.setTag(songNumber);
          this.updateProgressBar();
        });
      }
    },
    skipForward() {
      if (this.songCounter < this.playlist.length - 1) {
        this.songCounter++;
        this.playSelectedSong(this.songCounter);
      } else {
        this.songCounter === 0;
        this.playSelectedSong(this.songCounter);
      }
    },
    skipBackward() {
      if (this.songCounter > 0) {
        this.songCounter--;
        this.playSelectedSong(this.songCounter);
      }
    },
    playOrPause() {
      if (this.player.paused) {
        this.player.play();
        this.songPlaying = true;
      } else {
        this.player.pause();
        this.songPlaying = false;
      }
    },
    updateProgressBar() {
      this.player.addEventListener("timeupdate", () => {
        let cTime = this.player.currentTime / this.player.duration;
        if (this.progressSlider <= 100) {
          this.progressSlider = cTime * 100;
          this.getCurrentSeconds(Math.round(this.player.currentTime));
          this.getSongDuration(Math.round(this.player.duration));
        } else {
          this.progressSlider = 0;
        }
      });
    },
    getCurrentSeconds(seconds) {
      let min = Math.floor(seconds / 60);
      let sec = seconds % 60;
      min = min < 10 ? "0" + min : min;
      sec = sec < 10 ? "0" + sec : sec;
      this.currentSeconds = min + ":" + sec;
    },
    getSongDuration(seconds) {
      let min = Math.floor(seconds / 60);
      let sec = seconds % 60;
      min = min < 10 ? "0" + min : min;
      sec = sec < 10 ? "0" + sec : sec;
      this.songDuration = min + ":" + sec;
    },
    setTag(songNumber) {
      this.trackDetails.title = this.playlist[songNumber].title;
      this.trackDetails.album = this.playlist[songNumber].album;
      this.trackDetails.artist = this.playlist[songNumber].artist;
      this.trackDetails.coverArt = this.playlist[songNumber].coverArt;
    },
    adjustVolume() {
      this.player.volume = this.volumeSlider;
      this.muted = false;
    },
    muteAudio() {
      this.isMuted = !this.isMuted;
      this.player.muted = this.isMuted;
    },
    adjustProgressSlider() {
      let seekto = this.player.duration * (this.progressSlider / 100);
      this.player.currentTime = seekto;
    },
  },
  created() {
    setTimeout(() => {
      this.playAll();
    }, 1500);
  },
  watch: {
    selectedSong() {
      let select = this.selectedSong;
      this.playSelectedSong(select);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container .player-interface {
  background-color: rgb(82, 82, 82);
  color: rgb(212, 212, 212);
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  border-radius: 0px 15px 0px 15px;
  display: flex;
}

.container .player-interface .album-cover img {
  display: block;
  max-width: 250px;
  max-height: 125px;
  width: auto;
  height: auto;
  border-radius: 0px 15px 0px 15px;
  background: radial-gradient(transparent, black);
}

.wrapper {
  display: flex;
  flex-direction: column;
  padding-left: 5px;
}
.player-interface .track-info {
  margin-bottom: 2px;
}
.player-interface .track-info .title {
  font-weight: bold;
  font-size: large;
}

.player-interface .track-info .artist {
  font-weight: 250;
}

.player-interface .track-info .album {
  font-weight: 225;
}

.current-seconds,
.final-seconds {
  margin-left: 2px;
  font-size: smaller;
}

.main-btn {
  display: flex;
}

.seekbar {
  -webkit-appearance: none;
  -moz-appearance: none;
  width: 30vw;
  border: none;
  appearance: none;
  background-color: transparent;
}

.seekbar::-webkit-slider-runnable-track {
  height: 6px;
  background: rgb(122, 122, 122);
  position: relative;
  bottom: 3px;
  border-radius: 5px;
}

.seekbar::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  border-radius: 50%;
  height: 15px;
  width: 15px;
  position: relative;
  bottom: 5px;
  background: rgb(63, 63, 63);
  background-size: 50%;
  cursor: grab;
}

.seekbar::-moz-range-track {
  outline: none;
  appearance: none;
  height: 6px;
  background: rgb(122, 122, 122);
  position: relative;
  bottom: 3px;
  border-radius: 5px;
}

.seekbar::-moz-range-thumb {
  appearance: none;
  border: none;
  border-radius: 50%;
  height: 15px;
  width: 15px;
  position: relative;
  bottom: 5px;
  background: rgb(63, 63, 63);
  background-size: 50%;
  cursor: grab;
}

.seekbar::-ms-track {
  background: transparent;
  border-color: transparent;
  color: transparent;
  height: 6px;
  border-radius: 5px;
  cursor: grab;
}

.seekbar::-ms-fill-lower {
  background: #777;
  border-radius: 10px;
}

.player-control .progress-bar .seekbar::-ms-fill-upper {
  background: #ddd;
  border-radius: 10px;
}

.seekbar::-ms-thumb {
  border: none;
  cursor: grab;
  height: 15px;
  width: 15px;
  border-radius: 50%;
}

.seekbar:focus::-ms-fill-lower {
  background: #888;
}
.seekbar:focus::-ms-fill-upper {
  background: rgb(122, 122, 122);
}
.main-btn > div {
  padding: 8px;
}

.main-btn .btn .fa:hover {
  cursor: pointer;
  color: white;
  transform: scale(1.12);
  transition: 0.2s;
}

.main-btn .btn .fa.fa-play {
  font-size: 1.34em;
  line-height: 25px;
}

.vol-slider {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  width: 8vw;
  background-color: transparent;
}

.vol-slider::-webkit-slider-runnable-track {
  -webkit-appearance: none;
  appearance: none;
  height: 3px;
  background: rgb(122, 122, 122);
  position: relative;
  bottom: 3px;
}

.vol-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  border-radius: 50%;
  height: 15px;
  width: 15px;
  position: relative;
  bottom: 6px;
  background: rgb(63, 63, 63);
  background-size: 50%;
  cursor: grab;
}

.vol-slider::-moz-range-track {
  outline: none;
  appearance: none;
  height: 3px;
  background: rgb(122, 122, 122);
}

.vol-slider::-moz-range-thumb {
  appearance: none;
  border: none;
  border-radius: 50%;
  height: 15px;
  width: 15px;
  position: relative;
  bottom: 6px;
  background: rgb(63, 63, 63);
  background-size: 50%;
  cursor: grab;
}

.vol-slider::-ms-track {
  background: transparent;
  border-color: transparent;
  color: transparent;
  height: 3px;
  cursor: grab;
}

.vol-slider::-ms-fill-lower {
  background: #777;
  border-radius: 10px;
}

.vol-slider::-ms-fill-upper {
  background: #ddd;
  border-radius: 10px;
}

.vol-slider::-ms-thumb {
  border: none;
  cursor: grab;
  height: 15px;
  width: 15px;
  border-radius: 50%;
  background: rgb(63, 63, 63);
}

.vol-slider:focus::-ms-fill-lower {
  background: #888;
}

.vol-slider:focus::-ms-fill-upper {
  background: rgb(122, 122, 122);
}

@media only screen and (max-width: 320px) {
  .container {
    width: 100%;
    font-size: 90%;
  }
  .track-info .title,
  .album {
    white-space: nowrap;
    width: 50vw;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .seekbar {
    width: 25vw;
  }
  .vol-slider {
    width: 15vw;
  }
}

/* Smaller Mobile Devices */
@media only screen and (max-width: 320px) {
  .container {
    width: 100%;
  }
  .track-info {
    font-size: 90%;
  }
  .track-info .title,
  .album {
    white-space: nowrap;
    width: 50vw;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .seekbar {
    width: 30vw;
  }
  .main-btn .btn .fa.fa-play {
    line-height: 20px;
  }
  .vol-slider {
    width: 15vw;
  }
}

/*  Mobile devices */
@media only screen and (min-width: 321px) and (max-width: 480px) {
  .container {
    width: 100%;
  }
  .track-info .title,
  .album {
    white-space: nowrap;
    width: 50vw;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .seekbar {
    width: 35vw;
  }
  .vol-slider {
    width: 20vw;
  }
}

/*  iPads, Tablets */
@media only screen and (min-width: 481px) and (max-width: 768px) {
  .container {
    width: 100%;
  }
  .track-info .title,
  .album {
    white-space: nowrap;
    width: 70vw;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .seekbar {
    width: 55vw;
  }
  .vol-slider {
    width: 20vw;
  }
}

/*  Small screens, laptops*/
@media only screen and (min-width: 769px) and (max-width: 1024px) {
  .track-info .title,
  .album {
    white-space: nowrap;
    width: 25vw;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .seekbar {
    width: 20vw;
  }
  .vol-slider {
    width: 15vw;
  }
}

/*Desktops, large screens*/
@media only screen and (min-width: 1025px) and (max-width: 1280px) {
  .track-info .title,
  .album {
    white-space: nowrap;
    width: 35vw;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .seekbar {
    width: 25vw;
  }
  .vol-slider {
    width: 10vw;
  }
}
</style>
