<template>
    <div class="modal-content fm-modal-upload">
        <v-card-title class="headline">
            {{ lang.modal.upload.title }}
        </v-card-title>
        <v-card-text>
            <div class="fm-btn-wrapper" v-show="!progressBar">
                <v-btn flat class="grey btn-block">
                    {{ lang.btn.uploadSelect }}
                </v-btn>
                <input type="file" multiple name="myfile" v-on:change="selectFiles($event)">
            </div>
            <div class="fm-upload-list" v-if="countFiles">
                <v-flex
                     v-for="(item, index) in newFiles"
                     v-bind:key="index">
                        <i class="material-icons" v-bind:class="mimeToIcon(item.type)"></i>
                        {{ item.name }}
                        <span class="right">{{ bytesToHuman(item.size) }}</span>
                </v-flex>
                <v-flex>
                        <strong>{{ lang.modal.upload.selected }}</strong>
                        {{ newFiles.length }}
                </v-flex>
                <v-flex>
                        <strong>{{ lang.modal.upload.size }}</strong>
                        {{ allFilesSize }}
                </v-flex>
                <v-flex>
                      <strong>{{ lang.modal.upload.ifExist }}</strong>
                      <v-radio-group v-model="overwrite" :mandatory="false" row>
                      <v-radio :label="lang.modal.upload.skip" value="0"></v-radio>
                      <v-radio :label="lang.modal.upload.overwrite" value="1"></v-radio>
                    </v-radio-group>
                </v-flex>
            </div>
            <div v-else><p>{{ lang.modal.upload.noSelected }}</p></div>
            <div class="fm-upload-info">
                <!-- Progress Bar -->
                <div class="progress" v-show="countFiles">
                    <div class="progress-bar progress-bar-striped bg-info" role="progressbar"
                         v-bind:aria-valuenow="progressBar"
                         aria-valuemin="0"
                         aria-valuemax="100"
                         v-bind:style="{width: progressBar + '%' }">
                        {{ progressBar }}%
                    </div>
                </div>
            </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat v-on:click="hideModal()">{{ lang.btn.cancel }}</v-btn>
          <v-btn color="blue darken-1" flat v-bind:class="[countFiles ? '' : 'btn-default']"
                    v-bind:disabled="!countFiles"
                    v-on:click="uploadFiles">{{ lang.btn.submit }}</v-btn>
        </v-card-actions>
    </div>
</template>

<script>
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';
import helper from './../../../mixins/helper';

export default {
  name: 'Upload',
  mixins: [modal, translate, helper],
  data() {
    return {
      // selected files
      newFiles: [],

      // overwrite if exists
      overwrite: 0,
    };
  },
  computed: {

    /**
     * Progress bar value - %
     * @returns {number}
     */
    progressBar() {
      return this.$store.state.fm.messages.actionProgress;
    },

    /**
     * Count of files selected for upload
     * @returns {number}
     */
    countFiles() {
      return this.newFiles.length;
    },

    /**
     * Calculate the size of all files
     * @returns {*|string}
     */
    allFilesSize() {
      let size = 0;

      for (let i = 0; i < this.newFiles.length; i += 1) {
        size += this.newFiles[i].size;
      }

      return this.bytesToHuman(size);
    },

  },
  methods: {
    /**
     * Select file or files
     * @param event
     */
    selectFiles(event) {
      // files selected?
      if (event.target.files.length === 0) {
        // no file selected
        this.newFiles = [];
      } else {
        // we have file or files
        this.newFiles = event.target.files;
      }
    },

    /**
     * Upload new files
     */
    uploadFiles() {
      // if files exists
      if (this.countFiles) {
        // upload files
        this.$store.dispatch('fm/upload', {
          files: this.newFiles,
          overwrite: this.overwrite,
        }).then((response) => {
          // if upload is successful
          if (response.data.result.status === 'success') {
            // close modal window
            this.hideModal();
          }
        });
      }
    },
  },
};
</script>

<style lang="scss">
    .fm-modal-upload {

        .fm-btn-wrapper {
            position: relative;
            overflow: hidden;
            padding-bottom: 6px;
            margin-bottom: 0.6rem;
        }

        .fm-btn-wrapper input[type=file] {
            font-size: 100px;
            position: absolute;
            left: 0;
            top: 0;
            opacity: 0;
            cursor: pointer;
        }

        .fm-upload-list .far {
            padding-right: 0.5rem;
        }

        .fm-upload-list .form-check-inline {
            margin-right: 0;
        }

        .fm-upload-info > .progress {
            margin-bottom: 1rem;
        }
    }
</style>
