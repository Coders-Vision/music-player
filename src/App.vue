<template>
  <div class="app">
    <div class="container">
      <Player :playlist="playlist" :selectedSong="selectedSong" />
      <PlayList
        :trackList="audioFiles"
        @receivedPlaylist="sendPlaylist"
        @receivedSelectedSong="sendSelectedSong"
      />
    </div>
  </div>
</template>

<script>
import Player from "./components/player.vue";
import PlayList from "./components/playlist.vue";

export default {
  name: "App",
  components: {
   Player,
    PlayList
  },
  data() {
    return {
      audioFiles: [],
      playlist: [],
      selectedSong: 0
    };
  },
  methods: {
    getAudioFiles() {
      let songFolder = require.context("@/assets/songs", true, /\.(mp3)$/);
      songFolder.keys().forEach((filename) => {
        this.audioFiles.push(songFolder(filename));
      });
    },
    sendPlaylist(arrPlaylist) {
      this.playlist = arrPlaylist;
    },
    sendSelectedSong(song) {
      this.selectedSong = song;
    }
  },
  created() {

     this.getAudioFiles();

  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  box-sizing: border-box;
  list-style-type: none;
}
input {
  outline: none;
  appearance: none;
}

body,
html {
  height: 100%;
}
::-webkit-scrollbar {
  width: 5px;
  cursor: pointer;
  background-color: rgb(175, 175, 175);
}

::-webkit-scrollbar-track {
  border-radius: 2px;
  cursor: pointer;
}

::-webkit-scrollbar-thumb {
  border-radius: 2px;
  cursor: pointer;
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
}

.app {
  background-color: rgba(34, 34, 34, 0.867);
}

.container {
  margin: auto;
  width: 50%;
  background-color: rgb(46, 46, 46);
  height: 100vh;
  padding: 10px;
}

/* ==========For Player Component========== */

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
