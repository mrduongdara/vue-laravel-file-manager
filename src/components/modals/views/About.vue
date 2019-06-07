<template>
    <div class="modal-content fm-modal-about">
        <v-card-title class="headline">
            {{ lang.modal.about.title }}
        </v-card-title>
        <v-card-text>
            <strong>{{ lang.modal.about.name }}</strong>
            <hr>
            <v-layout wrap>
                <dt class="col s3">{{ lang.modal.about.version }}:</dt>
                <dd class="col s9">{{ version }}</dd>

                <dt class="col s3">{{ lang.modal.about.developer }}:</dt>
                <dd class="col s9">Aleksandr Manekin (alexusmai@gmail.com)</dd>

                <dt class="col s3">GitHub:</dt>
                <dd class="col s9">
                    <a href="https://github.com/alexusmai/laravel-file-manager" target="_blank">
                        alexusmai/laravel-file-manager
                    </a>
                </dd>
            </v-layout>
        </v-card-text>
    </div>
</template>

<script>
import modal from './../mixins/modal';
import translate from './../../../mixins/translate';

export default {
  name: 'About',
  mixins: [modal, translate],
  computed: {
    /**
     * App version
     * @returns {*}
     */
    version() {
      return this.$store.state.fm.settings.version;
    },
  },
};
</script>
