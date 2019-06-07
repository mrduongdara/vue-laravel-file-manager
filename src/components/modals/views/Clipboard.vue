<template>
    <div class="modal-content fm-modal-clipboard">
        <v-card-title class="headline">
            {{ lang.clipboard.title }}
        </v-card-title>
        <v-card-text>
            <template v-if="clipboard.type">
                <div class="d-flex justify-content-between">
                    <div class="w-75 text-truncate">
                        <span>
                           <i class="material-icons">storage</i>{{ clipboard.disk }}
                        </span>
                    </div>
                    <div class="text-right text-muted">
                        <span v-bind:title="`${lang.clipboard.actionType} - ${lang.clipboard[clipboard.type]}`">
                            <i v-if="clipboard.type === 'copy'" class="material-icons">content_copy</i>
                            <i v-else class="material-icons">content_cut</i>
                        </span>
                    </div>
                </div>
                <hr>
                <div class="d-flex justify-content-between"
                     v-for="(dir, index) in directories"
                     v-bind:key="`d-${index}`">
                    <div class="w-75 text-truncate">
                        <span>
                            <i class="material-icons">folder</i>{{ dir.name }}
                        </span>
                    </div>
                    <div class="text-right">
                        <v-btn flat icon
                                v-bind:title="lang.btn.delete"
                                v-on:click="deleteItem('directories', dir.path)">
                            <span aria-hidden="true">&times;</span>
                        </v-btn>
                    </div>
                </div>
                <div class="d-flex justify-content-between"
                     v-for="(file, index) in files"
                     v-bind:key="`f-${index}`">
                    <div class="w-75 text-truncate">
                        <span>
                            <i class="material-icons" v-bind:class="file.icon"></i>{{ file.name }}
                        </span>
                    </div>
                    <div class="text-right">
                        <v-btn flat icon
                                v-bind:title="lang.btn.delete"
                                v-on:click="deleteItem('files', file.path)">
                            <span aria-hidden="true">&times;</span>
                        </v-btn>
                    </div>
                </div>
            </template>
            <template v-else>
                <span>{{ lang.clipboard.none }}</span>
            </template>
        </v-card-text>
        <div class="modal-footer">
            <v-btn flat icon class="red"
                    v-bind:disabled="!clipboard.type"
                    v-on:click="resetClipboard">{{ lang.btn.clear }}
            </v-btn>
            <v-btn flat icon class="btn-default" v-on:click="hideModal">{{ lang.btn.cancel }}</v-btn>
        </div>
    </div>
</template>

<script>
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';
import helper from './../../../mixins/helper';

export default {
  name: 'Clipboard',
  mixins: [modal, translate, helper],
  computed: {
    /**
     * Clipboard state
     * @returns {*}
     */
    clipboard() {
      return this.$store.state.fm.clipboard;
    },

    /**
     * Paths and names for directories
     * @returns {{path: *, name: *}[]}
     */
    directories() {
      return this.$store.state.fm.clipboard.directories.map(item => ({
        path: item,
        name: item.split('/').slice(-1)[0],
      }));
    },

    /**
     * File names, paths and icons
     * @returns {{path: *, name: *, icon: *}[]}
     */
    files() {
      return this.$store.state.fm.clipboard.files.map((item) => {
        const name = item.split('/').slice(-1)[0];
        return {
          path: item,
          name,
          icon: this.extensionToIcon(name.split('.').slice(-1)[0]),
        };
      });
    },
  },
  methods: {
    /**
     * Delete item from clipboard
     * @param type
     * @param path
     */
    deleteItem(type, path) {
      this.$store.commit('fm/truncateClipboard', { type, path });
    },

    /**
     * Reset clipboard
     */
    resetClipboard() {
      this.$store.commit('fm/resetClipboard');
    },
  },
};
</script>

<style lang="scss">
    .fm-modal-clipboard {

        .modal-body .far {
            padding-right: 0.5rem;
        }
    }
</style>
