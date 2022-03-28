<template>
  <div>
    <div class="playersBlock" :ref="'playersBlock'">
      <player-component :source="this.leftSource ? this.leftSource : 'unknown'" id="leftPlayer"
                        @nextTrack="nextTrack"/>
      <player-component :source="this.rightSource? this.rightSource : 'unknown'" id="rightPlayer"/>
    </div>
    <div class="playersControl">
      <span @click="pausePlay" ref="playButton">▶</span>️
      <span @click="playersRemote(false)">⏪</span>
      <span @click="playersRemote">⏩</span>️ ️
      <span @click="nextTrack(false)">⏮</span>
      <span @click="nextTrack">⏭</span>️
      <span @click="setVolume($event, 'left')" ref="leftVolume">🔇</span>️
      <span @click="setVolume($event, 'right')" ref="rightVolume">🔇</span>️

    </div>
    <div v-if="visiblePlaylistsTool" class="playlistsTools">
      <div class="sideTools">
        <!--        LeftSide-->
        <div class="leftSide">
          <select-playlist
              :playlists="Object.keys(source)"
              side="left"
              @setSideSelectOptions="setSideSelectOptions"
              :checked="leftCheckedPlaylist"/>
          <playlist-builder :selectOptions="leftSelectOptions"
                            @setPlaylist="setPlaylist"
                            @deleteItemPlaylist="deleteItemPlaylist"
                            side="left"
                            :url="url"
                            :selectedOptions="leftPlaylist"/>
        </div>
        <div class="leftSide">
          <select-playlist :playlists="Object.keys(source)"
                           side="right"
                           @setSideSelectOptions="setSideSelectOptions"
                           :checked="rightCheckedPlaylist"/>
          <playlist-builder :selectOptions="rightSelectOptions"
                            @setPlaylist="setPlaylist"
                            :url="url"
                            @deleteItemPlaylist="deleteItemPlaylist"
                            side="right"
                            :selectedOptions="rightPlaylist"/>
        </div>
      </div>
      <div>
      </div>

    </div>
    <div class="bottomButtons">
      <button @click="saveConfig">save</button>
      <button @click="setDefaults">discard</button>
    </div>

  </div>

</template>

<script>
import SelectPlaylist from "@/components/selectPlaylist";
import PlayerComponent from "@/components/playerComponent";
import PlaylistBuilder from "@/components/playlistBuilder";

export default {
  components: {
    PlaylistBuilder,
    PlayerComponent,
    SelectPlaylist,
  },
  mounted: function () {
    this.setDefaults()
  },
  data() {
    return {
      url: 'https://www.murmuriki.ru/footage/',
      source: {
        'one': ['6_1', '6_2', '6_4', '6_3'],
        'two': ['blue1', 'blue2', 'blue3', 'blue4'],
        'tree': ['green1', 'green2', 'green3', 'green4'],
      },
      leftIndexSource: 0,
      rightIndexSource: 0,
      rightCheckedPlaylist: 1,
      leftCheckedPlaylist: 0,
      rightSelectOptions: [],
      leftSelectOptions: [],
      visiblePlaylistsTool: true,
      rightPlaylist: [],
      leftPlaylist: [],
      leftSource: null,
      rightSource: null
    }
  },
  methods: {
    setDefaults() {
      this.leftIndexSource = 0
      this.rightIndexSource = 0
      this.rightCheckedPlaylist = 1
      this.leftCheckedPlaylist = 0
      let rightSelectOptions = this.source[Object.keys(this.source)[1]]
      let leftSelectOptions = this.source[Object.keys(this.source)[0]]
      this.rightPlaylist = [rightSelectOptions[0]]
      this.leftPlaylist = [leftSelectOptions[0]]
      this.rightSelectOptions = rightSelectOptions
      this.leftSelectOptions = leftSelectOptions
      this.saveConfig()
    },
    saveConfig() {
     if(this.checkPlaylists()) {
       let players = this.getNodes('Player')
       this.leftSource = this.url + this.leftPlaylist[0] + '.mp4'
       this.rightSource = this.url + this.rightPlaylist[0] + '.mp4'
       players[0].load();
       players[1].load();
     } else {
       this.showError();
     }
    },
    checkPlaylists(){
      if(this.leftPlaylist.includes(null) || this.rightPlaylist.includes(null)){
        return false
      }
      return true
    },
    showError(){
      alert('Остались блоки с невыбранными эпизодами. Удалите их или выберете эпизоды')
    },
    setPlaylist(value, side) {
      this[side + 'Playlist'] = value;
    },
    deleteItemPlaylist(value, side) {
      let index = this[side + 'Playlist'].indexOf(value);
      if (index) {
        this[side + 'Playlist'].splice(index, 1);
      }
    },
    changeSource(next) {
      if (next) {
        this.leftIndexSource = this.leftIndexSource === this.leftPlaylist.length - 1 ? 0 : this.leftIndexSource + 1
        this.rightIndexSource = this.rightIndexSource === this.rightPlaylist.length - 1 ? 0 : this.rightIndexSource + 1
      } else {
        this.leftIndexSource > 0 ? this.leftIndexSource-- : null
        this.rightIndexSource > 0 ? this.rightIndexSource-- : null
      }
    },
    nextPlay(player, side) {
      player.src = this.url + this[side + 'Playlist'][this[side + 'IndexSource']] + '.mp4'
      player.load();
      player.play();
    },
    nextTrack(next = true) {
      let players = this.getNodes('Player');
      this.changeSource(next)
      this.nextPlay(players[0], 'left')
      this.nextPlay(players[1], 'right')
    },
    getNodes(nodeName) {
      let nodes = []
      nodes.push(document.getElementById('left' + nodeName))
      nodes.push(document.getElementById('right' + nodeName))
      return nodes
    },
    pausePlay() {
      let players = this.getNodes('Player')
      if (players[0].paused == true) {
        this.visiblePlaylistsTool = false
        this.$refs.playButton.innerHTML = '⏸'
        players[0].play()
        players[1].play()
      } else {
        this.visiblePlaylistsTool = true
        this.$refs.playButton.innerHTML = '▶'
        players[0].pause()
        players[1].pause()
      }
    },
    playersRemote(forward = true) {
      let players = this.getNodes('Player')
      if (forward) {
        players[0].currentTime += 10
      } else {
        players[0].currentTime -= 10
      }
      players[1].currentTime = players[0].currentTime
    },
    setVolume(event, side) {
      let players = this.getNodes('Player')
      let currentPlayer = side === 'left' ? players[0] : players[1]
      let differentPlayer = side === 'left' ? players[1] : players[0]
      let differentPlayerIcon = side === 'left' ? 'rightVolume' : 'leftVolume'
      if (currentPlayer.volume > 0) {
        currentPlayer.volume = 0
        event.target.innerHTML = '🔇'
      } else {
        currentPlayer.volume = 1
        differentPlayer.volume = 0
        this.$refs[differentPlayerIcon].innerHTML = '🔇'
        event.target.innerHTML = '🔊'
      }
    },
    setSideSelectOptions(value, side) {
      this.rightPlaylist = [],
          this.leftPlaylist = [],
          value = Number(value);
      let selectedSide = side + 'CheckedPlaylist'
      let differentSide = side === 'left' ? 'right' : 'left'
      let selectedDifferentSide = differentSide + 'CheckedPlaylist'
      this[selectedSide] = value
      if (this[selectedDifferentSide] === value) {
        this[selectedDifferentSide] = this[selectedDifferentSide] < Object.keys(this.source).length - 1 ?
            value + 1 : value - 1
      }
      this[side + 'SelectOptions'] = this.source[Object.keys(this.source)[value]]
      this[differentSide + 'SelectOptions'] = this.source[Object.keys(this.source)[this[selectedDifferentSide]]]
      this[side + 'Playlist'] = ['this']
      this[side + 'Playlist'] = [this[side + 'SelectOptions'][0]]
      this[differentSide + 'Playlist'] = [this[differentSide + 'SelectOptions'][0]]
      this.saveConfig()
    },
    addNewVideo(video, side) {
      this[side] = video
    }
  },
  name: 'App'
}
</script>

<style>
* {
  padding: 0;
  margin: 0;
}

h4 {
  margin: 20px 10px;
}

.sideTools {
  display: flex;
  justify-content: space-between;
  width: 50%;
}

.bottomButtons {
  display: flex;
  justify-content: end;
}

button {
  margin: 10px;
}

.selectPlaylistItem, select {
  margin: 5px 20px
}

h4 {
  margin: 10px
}

label {
  margin: 0 10px;
}

.playersBlock {
  width: 90%;
  align-items: center;
  display: flex;
  justify-content: center;
  margin: auto;
}

.playersControl {
  margin: 10px;
  text-align: center;
  font-size: 20px;
}

.playlistsTools .sideTools {
  margin: 50px;
}

.playerContainer, video {
  width: 600px;
  height: 400px;
}

.playlistsTools {
  display: flex;
  justify-content: center;
  align-items: center;
}

.tip,
span.support::before {
  display: none;
  position: absolute;
  z-index: 100;
  top: 20px;
  left: 0;
  background: #EDEDED;
  border-radius: 3px;
  border: 1px solid #ccc;
  box-shadow: 5px 5px 0.5em -0.1em rgba(0, 0, 6, 0.5);
  text-align: left;
  color: #000;
  font: normal 500 14px Arial, sans-serif;
  cursor: default;
  padding: 5px;
  width: 150px;
  min-height: 50px;
  height: auto;
}

a.support:focus .tip {
  display: block;
}


</style>