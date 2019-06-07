<template>
    <div class="modal-content fm-modal-rename">
        <v-card-title class="headline">
            {{ lang.modal.rename.title }}
        </v-card-title>
        <v-card-text>
            <div class="form-group">
                <label for="fm-input-rename">{{ lang.modal.rename.fieldName }}</label>
                <input type="text" class="form-control" id="fm-input-rename"
                       v-focus
                       v-bind:class="{'is-invalid': checkName}"
                       v-model="name"
                       v-on:keyup="validateName">
                <div class="invalid-feedback" v-show="checkName">
                    {{ lang.modal.rename.fieldFeedback }}
                    {{ directoryExist ? ` - ${lang.modal.rename.directoryExist}` : ''}}
                    {{ fileExist ? ` - ${lang.modal.rename.fileExist}` : ''}}
                </div>
            </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat v-on:click="hideModal">{{ lang.btn.cancel }}</v-btn>
          <v-btn color="blue darken-1" flat v-bind:disabled="!submitActive" v-on:click="rename">{{ lang.btn.submit }}</v-btn>
        </v-card-actions>
    </div>
</template>

<script>
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';

export default {
  name: 'Rename',
  mixins: [modal, translate],
  data() {
    return {
      name: '',
      directoryExist: false,
      fileExist: false,
    };
  },
  computed: {
    /**
     * Selected item
     * @returns {*}
     */
    selectedItem() {
      return this.$store.getters[`fm/${this.activeManager}/selectedList`][0];
    },

    /**
     * Check new name
     * @returns {boolean}
     */
    checkName() {
      return this.directoryExist || this.fileExist || !this.name;
    },

    /**
     * Submit button disable
     * @returns {*|boolean}
     */
    submitDisable() {
      return this.checkName || this.name === this.selectedItem.basename;
    },
  },
  mounted() {
    // initiate item name
    this.name = this.selectedItem.basename;
  },
  methods: {
    /**
     * Validate item name
     */
    validateName() {
      if (this.name !== this.selectedItem.basename) {
        // if item - folder
        if (this.selectedItem.type === 'dir') {
          // check folder name matches
          this.directoryExist = this.$store.getters[`fm/${this.activeManager}/directoryExist`](this.name);
        } else {
          // check file name matches
          this.fileExist = this.$store.getters[`fm/${this.activeManager}/fileExist`](this.name);
        }
      }
    },

    /**
     * Rename item
     */
    rename() {
      // create new name with path
      const newName = this.selectedItem.dirname ?
        `${this.selectedItem.dirname}/${this.name}` :
        this.name;

      this.$store.dispatch('fm/rename', {
        type: this.selectedItem.type,
        newName,
        oldName: this.selectedItem.path,
      }).then(() => {
        // close modal window
        this.hideModal();
      });
    },
  },
};
</script>
