<template>
  <div class="playlist">

    <div v-for="(tag, index) in playlist" :key="index" class="track-list">
      <ul>
        <li class="track-song" @click="sendSelected(index)">
          <div class="track-details">
            <div class="album-cover">
              <img class="album-art" :src="tag.coverArt" alt="album art" />
            </div>
            <div>
              <h5 class="track-title">{{ tag.title || "Unknown Title" }}</h5>
            </div>
            <div>
              <h5 class="track-artist">{{ tag.artist || "Unknown Artist" }}</h5>
            </div>
            <div>
              <h5 class="track-album">{{ tag.album || "Unknown Album" }}</h5>
            </div>
            <div>
              <h5 class="track-year">{{ tag.year || "Unknown Year" }}</h5>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
const mm = require("music-metadata-browser");

export default {
  props: {
    trackList: Array
  },
  data() {
    return {
      playlist: [],
      isSelected: false,
    };
  },

  methods: {
    getTracks() {
      this. trackList.forEach((songURL) => {
         this.parseAudioTags(songURL);
      });
    },
    parseAudioTags(url) {
      mm.fetchFromUrl(url)
       .then((tag) => {
          let coverArtPath = this.getCoverArt(tag);
          let songTag = this.constructPlaylist(url, tag, coverArtPath);
          this.playlist.push(songTag);
        })
        .catch((error) => console.error("Error Occured due to:" + error));
    },
    getCoverArt(Tag) {
      let unitarr = Tag.common.picture[0].data;
      let blobData = new Blob([unitarr], {
        type: Tag.common.picture[0].format
      });
      let urlCreator = window.URL || window.webkitURL;
      let imageUrl = urlCreator.createObjectURL(blobData);
      let img = new Image();
      let imgSource = (img.src = imageUrl);
      return imgSource;
    },

    constructPlaylist(songPath, currentTag, coverPath) {
      let track = {
        title: currentTag.common.title,
        artist: currentTag.common.artist,
        album: currentTag.common.album,
        year: currentTag.common.year,
        coverArt: coverPath,
        path: songPath
      };
      return track;
    },
    sendPlaylist() {
      this.$emit("receivedPlaylist", this.playlist);
    },
    sendSelected(song) {
      this.$emit("receivedSelectedSong", song);
    }
  },

  created() {
    this. getTracks()
    setTimeout(() => {
      this.sendPlaylist() 
    }, 1000);

  }
};
</script>

<style scoped>
.playlist {
  margin-top: 15px;
  background-color: rgb(82, 82, 82);
  color: rgb(212, 212, 212);
  display: block;
  width: 100%;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  border-radius: 0px 15px 0px 15px;
  position: relative;
  padding: 10px;
  overflow: auto;
  height: 75%;
}

.track-song {
  background: rgb(73, 73, 73);
  border-radius: 15px 15px 15px 15px;
  margin-bottom: 5px;
}

.track-song:hover {
  background: rgb(70, 70, 70);
  -webkit-transition: 0.15s ease-out;
  transition: 0.15s ease-out;
  cursor: pointer;
}
/* .active {
  background: rgb(49, 49, 49);
  -webkit-transition: 0.15s ease-out;
  transition: 0.15s ease-out;
  cursor: pointer;
} */
.container .playlist .track-details .album-cover img {
  width: 100px;
  height: 100px;
  display: block;
  border-radius: 15px 15px 15px 15px;
  background: radial-gradient(transparent, black);
}

.track-z {
  margin-bottom: 10px;
}

.track-details {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.track-details > * {
  flex: 1 1 100px;
}
.track-details .track-title {
  font-weight: 600;
  font-style: italic;
  text-align: left;
  margin-right: 2px;
}

.track-details .track-artist {
  font-weight: 400;
  text-align: left;
}

.track-details .track-album {
  font-weight: 400;
  font-style: italic;
  text-align: left;
  margin-right: 2px;
}
.track-details .track-year {
  text-align: left;
}

/* Smaller Mobile Devices */
@media only screen and (max-width: 320px) {
  .track-details > * {
    flex: 1 1 50px;
  }
  .container .playlist .track-details .album-cover img {
    width: 50px;
    height: 50px;
  }
  .track-details div h5 {
    font-size: 50%;
  }
}

/*  Mobile devices */
@media only screen and (min-width: 321px) and (max-width: 480px) {
  .track-details > * {
    flex: 1 1 50px;
  }
  .container .playlist .track-details .album-cover img {
    width: 50px;
    height: 50px;
  }
  .track-details div h5 {
    font-size: 50%;
  }
}

/*  iPads, Tablets */
@media only screen and (min-width: 481px) and (max-width: 768px) {
  .track-details > * {
    flex: 1 1 50px;
  }
  .container .playlist .track-details .album-cover img {
    width: 50px;
    height: 50px;
  }
  .track-details div h5 {
    font-size: 70%;
  }
}

/*  Small screens, laptops*/
@media only screen and (min-width: 769px) and (max-width: 1024px) {
  .track-details > * {
    flex: 1 1 50px;
  }
  .container .playlist .track-details .album-cover img {
    width: 50px;
    height: 50px;
  }
  .track-details div h5 {
    font-size: 70%;
  }
}

/*Desktops, large screens*/
@media only screen and (min-width: 1024px) and (max-width: 1280px) {
  .track-details > * {
    flex: 1 1 50px;
  }
  .track-details h5 {
    font-size: 100%;
  }
}
</style>
