<template>
    <div class="modal-content fm-modal-folder">
        <v-card-title class="headline">
            {{ lang.modal.newFile.title }}
        </v-card-title>
        <v-card-text>
                <v-text-field
                      v-focus
                      v-bind:class="{'is-invalid': fileExist}"
                      v-model="fileName"
                      v-on:keyup="validateFileName"
                      :label="lang.modal.newFile.fieldName"
                ></v-text-field>
                <div class="invalid-feedback" v-show="fileExist">
                    {{ lang.modal.newFile.fieldFeedback }}
                </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat v-on:click="hideModal">{{ lang.btn.cancel }}</v-btn>
          <v-btn color="blue darken-1" flat v-bind:disabled="!submitActive" v-on:click="addFile">{{ lang.btn.submit }}</v-btn>
        </v-card-actions>
    </div>
</template>

<script>
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';

export default {
  name: 'NewFile',
  mixins: [modal, translate],
  data() {
    return {
      // name for new file
      fileName: '',

      // file exist
      fileExist: false,
    };
  },
  computed: {
    /**
     * Submit button - active or no
     * @returns {string|boolean}
     */
    submitActive() {
      return this.fileName && !this.fileExist;
    },
  },
  methods: {
    /**
     * Check the file name if it exists or not.
     */
    validateFileName() {
      if (this.fileName) {
        this.fileExist = this.$store.getters[`fm/${this.activeManager}/fileExist`](this.fileName);
      } else {
        this.fileExist = false;
      }
    },

    /**
     * Create new file
     */
    addFile() {
      this.$store.dispatch('fm/createFile', this.fileName).then((response) => {
        // if new directory created successfully
        if (response.data.result.status === 'success') {
          // close modal window
          this.hideModal();
        }
      });
    },
  },
};
</script>
