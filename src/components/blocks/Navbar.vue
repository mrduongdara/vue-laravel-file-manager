<template>
    <v-layout finder-toolbar>
            <v-flex s6>
                <div class="left" role="group">
                    <v-btn flat icon outline
                            v-bind:disabled="backDisabled"
                            v-bind:title="lang.btn.back"
                            v-on:click="historyBack()">
                        <v-icon>keyboard_arrow_left</v-icon>
                    </v-btn>
                    <v-btn flat icon outline
                            v-bind:disabled="forwardDisabled"
                            v-bind:title="lang.btn.forward"
                            v-on:click="historyForward()">
                        <v-icon>keyboard_arrow_right</v-icon>
                    </v-btn>
                    <v-btn flat icon color="green"
                            v-on:click="refreshAll()"
                            v-bind:title="lang.btn.refresh">
                        <v-icon>sync</v-icon>
                    </v-btn>
                </div>
                <div class="left" role="group">
                    <v-btn flat icon class="grey"
                            v-on:click="showModal('NewFile')"
                            v-bind:title="lang.btn.file">
                        <v-icon>insert_drive_file</v-icon>
                    </v-btn>
                    <v-btn flat icon color="amber"
                            v-on:click="showModal('NewFolder')"
                            v-bind:title="lang.btn.folder">
                        <v-icon>folder</v-icon>
                    </v-btn>
                    <v-btn flat icon color="light-blue"
                            disabled
                            v-if="uploading"
                            v-bind:title="lang.btn.upload">
                        <v-icon>file_upload</v-icon>
                    </v-btn>
                    <v-btn flat icon color="light-blue"
                            v-else
                            v-on:click="showModal('Upload')"
                            v-bind:title="lang.btn.upload">
                        <v-icon>file_upload</v-icon>
                    </v-btn>
                    <v-btn flat icon color="red"
                            v-bind:disabled="!isAnyItemSelected"
                            v-on:click="showModal('Delete')"
                            v-bind:title="lang.btn.delete">
                        <v-icon>delete</v-icon>
                    </v-btn>
                </div>
                <div class="left" role="group">
                    <v-btn flat icon color="cyan"
                            v-bind:disabled="!isAnyItemSelected"
                            v-bind:title="lang.btn.copy"
                            v-on:click="toClipboard('copy')">
                        <v-icon>content_copy</v-icon>
                    </v-btn>
                    <v-btn flat icon color="cyan"
                            v-bind:disabled="!isAnyItemSelected"
                            v-bind:title="lang.btn.cut"
                            v-on:click="toClipboard('cut')">
                        <v-icon>content_cut</v-icon>
                    </v-btn>
                    <v-btn flat icon color="cyan"
                            v-bind:disabled="!clipboardType"
                            v-bind:title="lang.btn.paste"
                            v-on:click="paste">
                        <v-icon>content_paste</v-icon>
                    </v-btn>
                </div>
            </v-flex>
            <v-flex s6 text-sm-right>
                <v-btn flat icon outline
                        v-bind:class="[viewType === 'table' ? 'active' : '']"
                        v-on:click="selectView('table')"
                        v-bind:title="lang.btn.table">
                    <v-icon>format_list_bulleted</v-icon>
                </v-btn>
                <v-btn flat icon role="button" outline
                        v-bind:class="[viewType === 'grid' ? 'active' : '']"
                        v-on:click="selectView('grid')"
                        v-bind:title="lang.btn.grid">
                    <v-icon>view_comfy</v-icon>
                </v-btn>
            
                <v-btn flat icon outline
                        v-bind:title="lang.btn.fullScreen"
                        v-bind:class="{ active: fullScreen }"
                        v-on:click="screenToggle">
                    <v-icon>fullscreen</v-icon>
                </v-btn>
            </v-flex>
    </v-layout>
</template>

<script>
import translate from './../../mixins/translate';
import EventBus from './../../eventBus';

export default {
  mixins: [translate],
  computed: {
    /**
     * Active manager name
     * @returns {default.computed.activeManager|(function())|string|activeManager}
     */
    activeManager() {
      return this.$store.state.fm.activeManager;
    },

    /**
     * Back button state
     * @returns {boolean}
     */
    backDisabled() {
      return !this.$store.state.fm[this.activeManager].historyPointer;
    },

    /**
     * Forward button state
     * @returns {boolean}
     */
    forwardDisabled() {
      return this.$store.state.fm[this.activeManager].historyPointer ===
          this.$store.state.fm[this.activeManager].history.length - 1;
    },

    /**
     * Is any files or directories selected?
     * @returns {boolean}
     */
    isAnyItemSelected() {
      return this.$store.state.fm[this.activeManager].selected.files.length > 0 ||
          this.$store.state.fm[this.activeManager].selected.directories.length > 0;
    },

    /**
     * Manager view type - grid or table
     * @returns {default.computed.viewType|(function())|string}
     */
    viewType() {
      return this.$store.state.fm[this.activeManager].viewType;
    },

    /**
     * Upload files
     * @returns {boolean}
     */
    uploading() {
      return this.$store.state.fm.messages.actionProgress > 0;
    },

    /**
     * Clipboard - action type
     * @returns {null}
     */
    clipboardType() {
      return this.$store.state.fm.clipboard.type;
    },

    /**
     * Full screen status
     * @returns {default.computed.fullScreen|(function())|boolean|fullScreen|*|string}
     */
    fullScreen() {
      return this.$store.state.fm.fullScreen;
    },
  },
  methods: {
    /**
     * Refresh file manager
     */
    refreshAll() {
      this.$store.dispatch('fm/refreshAll');
    },

    /**
     * History back
     */
    historyBack() {
      this.$store.dispatch(`fm/${this.activeManager}/historyBack`);
    },

    /**
     * History forward
     */
    historyForward() {
      this.$store.dispatch(`fm/${this.activeManager}/historyForward`);
    },

    /**
     * Copy
     * @param type
     */
    toClipboard(type) {
      this.$store.dispatch('fm/toClipboard', type);

      // show notification
      if (type === 'cut') {
        EventBus.$emit('addNotification', {
          status: 'success',
          message: this.lang.notifications.cutToClipboard,
        });
      } else if (type === 'copy') {
        EventBus.$emit('addNotification', {
          status: 'success',
          message: this.lang.notifications.copyToClipboard,
        });
      }
    },

    /**
     * Paste
     */
    paste() {
      this.$store.dispatch('fm/paste');
    },

    /**
     * Show modal window
     * @param modalName
     */
    showModal(modalName) {
      // show selected modal
      this.$store.commit('fm/modal/setModalState', {
        modalName,
        show: true,
      });
    },

    /**
     * Select view type
     * @param type
     */
    selectView(type) {
      if (this.viewType !== type) this.$store.commit(`fm/${this.activeManager}/setView`, type);
    },

    /**
     * Full screen toggle
     */
    screenToggle() {
      const fm = document.getElementsByClassName('fm')[0];

      if (!this.fullScreen) {
        if (fm.requestFullscreen) {
          fm.requestFullscreen();
        } else if (fm.mozRequestFullScreen) {
          fm.mozRequestFullScreen();
        } else if (fm.webkitRequestFullscreen) {
          fm.webkitRequestFullscreen();
        } else if (fm.msRequestFullscreen) {
          fm.msRequestFullscreen();
        }
      } else if (document.exitFullscreen) {
        document.exitFullscreen();
      } else if (document.webkitExitFullscreen) {
        document.webkitExitFullscreen();
      } else if (document.mozCancelFullScreen) {
        document.mozCancelFullScreen();
      } else if (document.msExitFullscreen) {
        document.msExitFullscreen();
      }

      this.$store.commit('fm/screenToggle');
    },
  },
};
</script>

<style lang="scss">
.finder-toolbar {
  background: #ffffff;
  border-bottom: 1px solid #dddddd;
}
    .fm-navbar {

        .left {
            margin-right: 0.4rem;
        }
    }
</style>
