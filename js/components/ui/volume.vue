<template>
  <span class="volume control" id="volume">
    <i
      @click="unmute"
      class="fa fa-volume-off unmute"
      role="button"
      tabindex="0"
      title="Unmute"
      v-if="muted"
    ></i>
    <i
      @click="mute"
      class="fa fa-volume-up mute"
      role="button"
      tabindex="0"
      title="Mute"
      v-else
    ></i>
    <input
      @change="broadcastVolume"
      @input="setVolume"
      class="plyr__volume"
      id="volumeRange"
      max="10"
      step="0.1"
      title="Volume"
      type="range"
    >
  </span>
</template>

<script lang="ts">
import Vue from 'vue'
import { playback, socket } from '@/services'
import { events } from '@/config'

export default Vue.extend({
  data: () => ({
    muted: false
  }),

  methods: {
    mute (): void {
      this.muted = true
      playback.mute()
    },

    unmute (): void {
      this.muted = false
      playback.unmute()
    },

    setVolume (e: InputEvent): void {
      const volume = parseFloat((e.target as HTMLInputElement).value)
      playback.setVolume(volume)
      this.muted = volume === 0
    },

    /**
     * Broadcast the volume changed event to remote controller.
     */
    broadcastVolume: (e: InputEvent): void => {
      socket.broadcast(events.SOCKET_VOLUME_CHANGED, parseFloat((e.target as HTMLInputElement).value))
    }
  }
})
</script>

<style lang="scss">
#volume {
  position: relative;
  z-index: 99;

  // More tweaks
  [type=range] {
    margin: -1px 0 0 5px;
    transform: rotate(270deg);
    transform-origin: 0;
    position: absolute;
    bottom: -25px;
    border: 14px solid var(--color-bg-primary);
    border-left-width: 30px;
    z-index: 0;
    width: 140px;
    border-radius: 4px;
    display: none;
  }

  &:hover [type=range] {
    display: block;
  }

  i {
    width: 16px;
    position: relative;
    z-index: 1;
  }

  @media only screen and (max-width: 768px) {
    display: none !important;
  }
}
</style>
