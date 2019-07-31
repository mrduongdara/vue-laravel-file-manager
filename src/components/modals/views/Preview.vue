<template>
    <div class="modal-content fm-modal-preview">
        <v-card-title class="headline">
            
                {{ showCropperModule ? lang.modal.cropper.title : lang.modal.preview.title }}
                <small class="text-muted pl-3">{{ selectedItem.basename }}</small>
            
        </v-card-title>
        <div class="modal-body text-center">
            <template v-if="showCropperModule">
                <cropper-module v-bind:imgUrl="imgUrl"
                                v-bind:maxHeight="maxHeight"
                                v-on:closeCropper="closeCropper"></cropper-module>
            </template>
            <template v-else>
                <img v-bind:src="imgUrl"
                     v-bind:alt="selectedItem.basename"
                     v-bind:style="{'max-height': maxHeight+'px'}">
            </template>
        </div>
        <div v-if="showFooter" class="d-flex justify-content-between">
            <span class="d-block">
                <v-btn flat class=""
                        v-bind:title="lang.modal.cropper.title" v-on:click="showCropperModule = true">
                    <i class="material-icons">crop</i>
                </v-btn>
            </span>
            <span class="d-block">
                <v-btn flat class="btn-default" v-on:click="hideModal">{{ lang.btn.cancel }}</v-btn>
            </span>
        </div>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat v-on:click="hideModal">{{ lang.btn.cancel }}</v-btn>
        </v-card-actions>
    </div>
</template>

<script>
import CropperModule from './../additions/Cropper.vue';
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';
import helper from './../../../mixins/helper';

export default {
  name: 'Preview',
  mixins: [modal, translate, helper],
  components: { CropperModule },
  data() {
    return {
      showCropperModule: false,
      imgUrl: '',
    };
  },
  created() {
    // Create image URL
    this.setImgUrl();
  },
  computed: {
    /**
     * Selected disk
     * @returns {*}
     */
    selectedDisk() {
      return this.$store.getters['fm/selectedDisk'];
    },

    /**
     * Selected file
     * @returns {*}
     */
    selectedItem() {
      return this.$store.getters['fm/selectedItems'][0];
    },

    /**
     * Show modal footer
     * @return boolean
     */
    showFooter() {
      return this.canCrop(this.selectedItem.extension) && !this.showCropperModule;
    },

    /**
     * Calculate the max height for image
     * @returns {number}
     */
    maxHeight() {
      if (this.$store.state.fm.modal.modalBlockHeight) {
        return this.$store.state.fm.modal.modalBlockHeight - 170;
      }

      return 300;
    },
  },
  methods: {
    /**
     * Can we crop this image?
     * @param extension
     * @returns {boolean}
     */
    canCrop(extension) {
      return this.$store.state.fm.settings.cropExtensions.includes(extension.toLowerCase());
    },

    /**
     * Set image URL
     */
    setImgUrl() {
      this.imgUrl = `${this.$store.getters['fm/settings/baseUrl']}preview?disk=${this.selectedDisk}&path=${encodeURIComponent(this.selectedItem.path)}&v=${this.selectedItem.timestamp}`;
    },

    /**
     * Close cropper
     */
    closeCropper() {
      this.showCropperModule = false;
      this.setImgUrl();
    },
  },
};
</script>

<style lang="scss">
    .fm-modal-preview {

        .modal-body {
            padding: 0;

            img {
                max-width: 100%;
            }
        }

        & > .d-flex {
            padding: 1rem;
            border-top: 1px solid #e9ecef;
        }
    }
</style>
