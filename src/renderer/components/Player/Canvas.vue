<template>
  <div id="player-canvas"
    :class="{
      fullscreen: isFullscreen,
      maximize: isMaximize,
      fillscreen: osdMode && !isFullscreen
    }">
    <embed id="mpvjs" type="application/x-mpvjs" />
  </div>
</template>

<script>
const { remote } = require('electron')

export default {
  computed: {
    playerReady () {
      return this.$store.state.Player.playerReady
    },
    isFullscreen () {
      return this.$store.state.Window.isFullscreen
    },
    isMaximize () {
      return this.$store.state.Window.isMaximize
    },
    osdMode () {
      return this.$store.state.Settings.osdMode
    }
  },
  watch: {
    playerReady (value, _) {
      if (value) {
        this.loadFile()
      }
    }
  },
  methods: {
    loadFile () {
      // TODO: delete me
      // const files = [require('path').join(__dirname, '../../../../test.mp4')]
      // this.$store.dispatch('loadFiles', files)

      let dialog = remote.dialog
      let currentWindow = remote.getCurrentWindow()

      let options = {
        // See place holder 1 in above image
        title: 'Select Video',

        // See place holder 3 in above image
        buttonLabel: 'Load Video',

        // See place holder 4 in above image
        filters: [
          { name: 'Videos', extensions: ['mkv', 'avi', 'mp4'] },
          { name: 'All Files', extensions: ['*'] }
        ],
        properties: ['openFile', 'multiSelections']
      }

      // Synchronous
      let filePaths = dialog.showOpenDialog(currentWindow, options)

      this.$store.dispatch('loadFiles', filePaths)
    }
  },
  mounted () {
    this.$nextTick(function () {
      this.$store.dispatch('loadPlayer', document.getElementById('mpvjs'))
    })
  }
}
</script>

<style scoped>
#player-canvas {
  display: flex;
  position: absolute;
  height: calc(100% - 16px - 6rem);
  width: calc(100% - 16px);
  z-index: 1;
  margin-top: 2rem;
}
#mpvjs {
  flex: 1;
}

.maximize {
  height: calc(100% - 4px - 6rem) !important;
  width: calc(100% - 4px) !important;
}
.fullscreen {
  height: 100% !important;
  width: 100% !important;
  margin: 0 !important;
}
.fillscreen {
  height: calc(100% - 16px) !important;
  margin: 0 !important;
}
</style>
