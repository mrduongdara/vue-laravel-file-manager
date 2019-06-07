<template>
    <div class="modal-content fm-modal-errors">
        <v-card-title class="headline">
            {{ lang.modal.status.title }}
        </v-card-title>
        <v-card-text>
            <div v-if="errors.length">
                <ul class="list-unstyled">
                    <li v-for="(item, index) in errors" v-bind:key="index">
                        {{ item.status }} - {{ item.message }}
                    </li>
                </ul>
            </div>
            <div v-else>
                <span>{{ lang.modal.status.noErrors }}</span>
            </div>
        </v-card-text>
        <div class="modal-footer">
            <v-btn flat icon class="red"
                    v-bind:disabled="!errors.length"
                    v-on:click="clearErrors">{{ lang.btn.clear }}</v-btn>
            <v-btn flat icon class="btn-default" v-on:click="hideModal">{{ lang.btn.cancel }}</v-btn>
        </div>
    </div>
</template>

<script>
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';

export default {
  name: 'Status',
  mixins: [modal, translate],
  computed: {
    /**
     * Application errors
     * @returns {default.computed.errors|(function())|Array|boolean}
     */
    errors() {
      return this.$store.state.fm.messages.errors;
    },
  },
  methods: {
    /**
     * Clear all errors
     */
    clearErrors() {
      this.$store.commit('fm/messages/clearErrors');
    },
  },
};
</script>
