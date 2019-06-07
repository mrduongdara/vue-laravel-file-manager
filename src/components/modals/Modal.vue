<template>
    <v-layout row justify-center>
      <v-dialog name="fm-modal" v-model="dialog" ref="fmModal" persistent max-width="600" v-bind:class="modalSize"
                 v-on:click.stop>
        <v-card>
          <component v-bind:is="modalName"></component>
        </v-card>
      </v-dialog>
    </v-layout>
</template>

<script>
import NewFile from './views/NewFile.vue';
import NewFolder from './views/NewFolder.vue';
import Upload from './views/Upload.vue';
import Delete from './views/Delete.vue';
import Clipboard from './views/Clipboard.vue';
import Status from './views/Status.vue';
import Rename from './views/Rename.vue';
import Properties from './views/Properties.vue';
import Preview from './views/Preview.vue';
import TextEdit from './views/TextEdit.vue';
import AudioPlayer from './views/AudioPlayer.vue';
import VideoPlayer from './views/VideoPlayer.vue';
import Zip from './views/Zip.vue';
import Unzip from './views/Unzip.vue';
import About from './views/About.vue';

export default {
  name: 'Modal',
  components: {
    NewFile,
    NewFolder,
    Upload,
    Delete,
    Clipboard,
    Status,
    Rename,
    Properties,
    Preview,
    TextEdit,
    AudioPlayer,
    VideoPlayer,
    Zip,
    Unzip,
    About,
  },
  mounted() {
    // set height
    this.$store.commit('fm/modal/setModalBlockHeight', this.$refs.fmModal.offsetHeight);
  },
  computed: {
    /**
     * Selected modal name
     * @returns {null|*}
     */
    modalName() {
      return this.$store.state.fm.modal.modalName;
    },

    /**
     * Modal size style
     * @returns {{'modal-lg': boolean, 'modal-sm': boolean}}
     */
    modalSize() {
      return {
        'modal-xl': this.modalName === 'Preview' || this.modalName === 'TextEdit',
        'modal-lg': this.modalName === 'VideoPlayer',
        'modal-sm': false,
      };
    },
  },
  methods: {
    /**
     * Hide modal window
     */
    hideModal() {
      this.$store.commit('fm/modal/clearModal');
    },
  },
  data () {
    return {
      dialog: true
    }
  }
};
</script>

<style lang="scss">

    .fm-modal {
        position: absolute;
        z-index: 9998;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        width: 100%;
        height: 100%;
        background-color: rgba(0, 0, 0, .35);
        display: block;
        transition: opacity .4s ease;
        overflow: auto;

        .modal-xl {
            max-width: 96%;
        }
    }

    .fm-modal-enter-active, .fm-modal-leave-active {
        transition: opacity .5s;
    }
    .fm-modal-enter, .fm-modal-leave-to {
        opacity: 0;
    }
</style>
