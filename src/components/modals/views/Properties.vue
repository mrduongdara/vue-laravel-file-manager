<template>
    <div class="modal-content fm-modal-properties">
        <v-card-title class="headline">
            {{ lang.modal.properties.title }}
        </v-card-title>
        <v-card-text>
            <v-layout wrap>
                <v-flex s2>{{ lang.modal.properties.disk }}:</v-flex>
                <v-flex s9>{{ selectedDisk }}</v-flex>
                <v-flex s1>
                    <i v-on:click="copyToClipboard(selectedDisk)"
                       v-bind:title="lang.clipboard.copy"
                       class="material-icons">content_copy</i>
                </v-flex>
            </v-layout>
            <v-layout wrap>
                <v-flex s2>{{ lang.modal.properties.name }}:</v-flex>
                <v-flex s9>{{ selectedItem.basename }}</v-flex>
                <v-flex s1>
                    <i v-on:click="copyToClipboard(selectedItem.basename)"
                       v-bind:title="lang.clipboard.copy"
                       class="material-icons">content_copy</i>
                </v-flex>
            </v-layout>
            <v-layout wrap>
                <v-flex s2>{{ lang.modal.properties.path }}:</v-flex>
                <v-flex s9>{{ selectedItem.path }}</v-flex>
                <v-flex s1>
                    <i v-on:click="copyToClipboard(selectedItem.path)"
                       v-bind:title="lang.clipboard.copy"
                       class="material-icons">content_copy</i>
                </v-flex>
            </v-layout>
            <template v-if="selectedItem.type === 'file'">
                <v-layout wrap>
                    <v-flex s2>{{ lang.modal.properties.size }}:</v-flex>
                    <v-flex s9>{{ bytesToHuman(selectedItem.size) }}</v-flex>
                    <v-flex s1>
                        <i v-on:click="copyToClipboard(bytesToHuman(selectedItem.size))"
                           v-bind:title="lang.clipboard.copy"
                           class="material-icons">content_copy</i>
                    </v-flex>
                </v-layout>
                <v-layout wrap>
                    <v-flex s2>{{ lang.modal.properties.url }}:</v-flex>
                    <v-flex s9>
                        <span v-if="url">{{ url }}</span>
                        <span v-else>
                            <v-btn flat icon v-on:click="getUrl" type="button"
                                    class="btn-sm btn-light">
                                <i class="material-icons">insert_link</i> Get URL
                            </v-btn>
                        </span>
                    </v-flex>
                    <div v-if="url" class="col s1 text-right">
                        <i v-on:click="copyToClipboard(url)"
                           v-bind:title="lang.clipboard.copy"
                           class="material-icons">content_copy</i>
                    </div>
                </v-layout>
            </template>
            <template v-if="selectedItem.hasOwnProperty('timestamp')">
                <v-layout wrap>
                    <v-flex s2>{{ lang.modal.properties.modified }}:</v-flex>
                    <v-flex s9>{{ timestampToDate(selectedItem.timestamp) }}</v-flex>
                    <v-flex s1>
                        <i v-on:click="copyToClipboard(timestampToDate(selectedItem.timestamp))"
                           v-bind:title="lang.clipboard.copy"
                           class="material-icons">content_copy</i>
                    </v-flex>
                </v-layout>
            </template>
            <template v-if="selectedItem.hasOwnProperty('acl')">
                <v-layout wrap>
                    <v-flex s2>{{ lang.modal.properties.access }}:</v-flex>
                    <v-flex s9>{{ lang.modal.properties['access_' + selectedItem.acl] }}</v-flex>
                </v-layout>
            </template>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat v-on:click="hideModal">{{ lang.btn.cancel }}</v-btn>
        </v-card-actions>
    </div>
</template>

<script>
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';
import helper from './../../../mixins/helper';
import EventBus from './../../../eventBus';

export default {
  name: 'Properties',
  mixins: [modal, translate, helper],
  data() {
    return {
      url: null,
    };
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
  },
  methods: {
    /**
     * Get URL
     */
    getUrl() {
      this.$store.dispatch('fm/url', {
        disk: this.selectedDisk,
        path: this.selectedItem.path,
      }).then((response) => {
        if (response.data.result.status === 'success') {
          this.url = response.data.url;
        }
      });
    },

    /**
     * Copy text to clipboard
     * @param text
     */
    copyToClipboard(text) {
      // create input
      const copyInputHelper = document.createElement('input');
      copyInputHelper.className = 'copyInputHelper';
      document.body.appendChild(copyInputHelper);
      // add text
      copyInputHelper.value = text;
      copyInputHelper.select();
      // copy text to clipboard
      document.execCommand('copy');
      // clear
      document.body.removeChild(copyInputHelper);

      // Notification
      EventBus.$emit('addNotification', {
        status: 'success',
        message: this.lang.notifications.copyToClipboard,
      });
    },
  },
};
</script>

<style lang="scss">
    
</style>
