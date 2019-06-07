<template>
    <div class="modal-content fm-modal-folder">
        <v-card-title class="headline">
            {{ lang.modal.newFolder.title }}
        </v-card-title>
        <v-card-text>
                       <v-text-field
                       id="fm-folder-name"
                       v-focus
                       v-bind:class="{'is-invalid': directoryExist}"
                       v-model="directoryName"
                       v-on:keyup="validateDirName"
                          :label="lang.modal.newFolder.fieldName"
                        ></v-text-field>
                <div class="invalid-feedback" v-show="directoryExist">
                    {{ lang.modal.newFolder.fieldFeedback }}
                </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat v-on:click="hideModal">{{ lang.btn.cancel }}</v-btn>
          <v-btn color="blue darken-1" flat v-bind:disabled="!submitActive" v-on:click="addFolder">{{ lang.btn.submit }}</v-btn>
        </v-card-actions>
    </div>
</template>

<script>
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';

export default {
  name: 'NewFolder',
  mixins: [modal, translate],
  data() {
    return {
      // name for new directory
      directoryName: '',

      // directory exist
      directoryExist: false,
    };
  },
  computed: {
    /**
     * Submit button - active or no
     * @returns {string|boolean}
     */
    submitActive() {
      return this.directoryName && !this.directoryExist;
    },
  },
  methods: {
    /**
     * Check the folder name if it exists or not.
     */
    validateDirName() {
      if (this.directoryName) {
        this.directoryExist = this.$store.getters[`fm/${this.activeManager}/directoryExist`](this.directoryName);
      } else {
        this.directoryExist = false;
      }
    },

    /**
     * Create new directory
     */
    addFolder() {
      this.$store.dispatch('fm/createDirectory', this.directoryName).then((response) => {
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
