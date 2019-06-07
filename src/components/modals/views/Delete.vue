<template>
    <div class="modal-content fm-modal-delete">
        <v-card-title class="headline">
            {{ lang.modal.delete.title }}
        </v-card-title>
        <v-card-text>
            <div v-if="selectedItems.length">
                <selected-file-list></selected-file-list>
            </div>
            <div v-else>
                <span class="text-danger">{{ lang.modal.delete.noSelected }}</span>
            </div>
        </v-card-text>
        <div class="modal-footer">
            <v-btn flat icon class="red" v-on:click="deleteItems">{{ lang.modal.delete.title }}
            </v-btn>
            <v-btn flat icon class="btn-default" v-on:click="hideModal">{{ lang.btn.cancel }}</v-btn>
        </div>
    </div>
</template>

<script>
import SelectedFileList from '../additions/SelectedFileList.vue';
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';

export default {
  name: 'Delete',
  mixins: [modal, translate],
  components: { SelectedFileList },
  computed: {
    /**
     * Files and folders for deleting
     * @returns {*}
     */
    selectedItems() {
      return this.$store.getters['fm/selectedItems'];
    },
  },
  methods: {
    /**
     * Delete selected directories and files
     */
    deleteItems() {
      // create items list for delete
      const items = this.selectedItems.map(item => ({
        path: item.path,
        type: item.type,
      }));

      this.$store.dispatch('fm/delete', items).then(() => {
        // close modal window
        this.hideModal();
      });
    },
  },
};
</script>
