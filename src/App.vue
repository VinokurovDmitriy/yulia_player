<template>
  <div>
    <div class="playersBlock">
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
                            :selectedOptions="leftPlaylist"/>
        </div>
        <div class="leftSide">
          <select-playlist :playlists="Object.keys(source)"
                           side="right"
                           @setSideSelectOptions="setSideSelectOptions"
                           :checked="rightCheckedPlaylist"/>
          <playlist-builder :selectOptions="rightSelectOptions"
                            @setPlaylist="setPlaylist"
                            @deleteItemPlaylist="deleteItemPlaylist"
                            side="right"
                            :selectedOptions="rightPlaylist"/>
        </div>
      </div>
      <div>
      </div>
      <button v-if="rightPlaylist.length > 0 &&  leftPlaylist.length > 0" @click="saveConfig">save</button>
      <button v-if="rightPlaylist.length > 0 &&  leftPlaylist.length > 0" @click="discardConfig">discard</button>
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
  data() {
    return {
      isActive: false,
      url: 'https://www.murmuriki.ru/footage/',
      source: {
        0: {'6_1.mp4': {img: 'img.png'}, '6_6.mp4': {img: 'img.png'}},
        1: {'blue1.mp4': {img: 'img_2.png'}, 'blue2.mp4': {img: 'img_3.png'}},
        2: {'green1.mp4': {img: 'img_4.png'}, 'green2.mp4': {img: 'img_5.png'}},
      },
      leftIndexSource: 0,
      rightIndexSource: 0,
      rightCheckedPlaylist: 1,
      leftCheckedPlaylist: 0,
      rightSelectOptions: {'blue1.mp4': {img: 'img_2.png'}, 'blue2.mp4': {img: 'img_3.png'}},
      leftSelectOptions: {'6_1.mp4': {img: 'img.png'}, '6_6.mp4': {img: 'img.png'}},
      visiblePlaylistsTool: true,
      rightPlaylist: [],
      leftPlaylist: [],
      leftSource: null,
      rightSource: null
    }
  },
  methods: {
    saveConfig() {
      let players = this.getNodes('Player')
      console.log(this.leftPlaylist)
      console.log(this.rightPlaylist)
      this.leftSource = this.url + this.leftPlaylist[0]
      this.rightSource = this.url + this.rightPlaylist[0]
      players[0].load();
      players[1].load();
    },
    discardConfig() {
      this.leftSource = []
      this.rightSource = []
      this.rightPlaylist = []
      this.leftPlaylist = []
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
        this.leftIndexSource = this.leftIndexSource == this.leftPlaylist.length - 1 ? 0 : this.leftIndexSource + 1
        this.rightIndexSource = this.rightIndexSource == this.rightPlaylist.length - 1 ? 0 : this.rightIndexSource + 1
      } else {
        this.leftIndexSource > 0 ? this.leftIndexSource-- : null
        this.rightIndexSource > 0 ? this.rightIndexSource-- : null
      }
    },
    nextPlay(player, side) {
      player.src = this.url + this[side + 'Playlist'][this[side + 'IndexSource']]
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
      let different = side === 'left' ? 'right' : 'left'
      let differentSide = different + 'CheckedPlaylist'
      this[selectedSide] = value
      if (this[differentSide] === value) {
        this[differentSide] = this[differentSide] < Object.keys(this.source).length - 1 ?
            value + 1 : value - 1
      }
      this[side + 'SelectOptions'] = this.source[Object.keys(this.source)[value]]
      this[different + 'SelectOptions'] = this.source[Object.keys(this.source)[this[differentSide]]]
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

.sideTools {
  display: flex;
  justify-content: space-between;
  width: 50%;
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
</style>