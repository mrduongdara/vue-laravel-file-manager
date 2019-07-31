<template>
  <v-layout column fill-height md-finder
       v-bind:class="{ 'fm-full-screen': fullScreen }">
       <v-flex xs12>
    <navbar></navbar>
       </v-flex>
    <v-flex xs12>
      <context-menu></context-menu>
        <v-flex xs12>
      <notification></notification>
        </v-flex>
      <v-layout row fill-width>
      <modal v-if="showModal"></modal>
      <template v-if="windowsConfig === 1">
        <left-manager class="col" manager="left"></left-manager>
      </template>
      <template v-else-if="windowsConfig === 2">
        <v-flex xs4>
        <folder-tree></folder-tree>
        </v-flex>
        <v-flex xs8>
        <left-manager manager="left"></left-manager>
        </v-flex>
      </template>
      <template v-else-if="windowsConfig === 3">
        <v-flex xs6>
        <left-manager
                      manager="left"
                      v-on:click.native="selectManager('left')"
                      v-on:contextmenu.native="selectManager('left')">
        </left-manager>
        </v-flex>
        <v-flex xs6>
        <right-manager
                       manager="right"
                       v-on:click.native="selectManager('right')"
                       v-on:contextmenu.native="selectManager('right')">
        </right-manager>
        </v-flex>
      </template>
      </v-layout>
    </v-flex>
    <v-flex xs12>
    <info-block></info-block>
    </v-flex>
  </v-layout>
</template>

<script>
/* eslint-disable import/no-duplicates, no-param-reassign */
import { mapState } from 'vuex';
// Axios
import HTTP from './http/axios';
import EventBus from './eventBus';
// Components
import Navbar from './components/blocks/Navbar.vue';
import FolderTree from './components/tree/FolderTree.vue';
import LeftManager from './components/manager/Manager.vue';
import RightManager from './components/manager/Manager.vue';
import Modal from './components/modals/Modal.vue';
import InfoBlock from './components/blocks/InfoBlock.vue';
import ContextMenu from './components/blocks/ContextMenu.vue';
import Notification from './components/blocks/Notification.vue';

export default {
  name: 'FileManager',
  components: {
    Navbar,
    FolderTree,
    LeftManager,
    RightManager,
    Modal,
    InfoBlock,
    ContextMenu,
    Notification,
  },
  computed: {
    ...mapState('fm', {
      windowsConfig: state => state.settings.windowsConfig,
      activeManager: state => state.settings.activeManager,
      showModal: state => state.modal.showModal,
      fullScreen: state => state.settings.fullScreen,
    }),
  },
  created() {
    // initiate Axios settings - baseUrl and headers
    this.$store.commit('fm/settings/initAxiosSettings');

    // add axios request interceptor
    this.requestInterceptor();

    // add axios response interceptor
    this.responseInterceptor();

    // initialize app settings
    this.$store.dispatch('fm/initializeApp');

    /**
     * todo Keyboard event
     */
    /*
    window.addEventListener('keyup', (event) => {
      event.preventDefault();
      event.stopPropagation();

      EventBus.$emit('keyMonitor', event);
    });
    */
  },
  methods: {
    /**
     * Add axios request interceptor
     */
    requestInterceptor() {
      HTTP.interceptors.request.use((config) => {
        // overwrite base url
        config.baseURL = this.$store.getters['fm/settings/baseUrl'];

        // overwrite headers
        config.headers = this.$store.getters['fm/settings/headers'];

        // loading spinner +
        this.$store.commit('fm/messages/addLoading');

        return config;
      }, (error) => {
        // loading spinner -
        this.$store.commit('fm/messages/subtractLoading');
        // Do something with request error
        return Promise.reject(error);
      });
    },

    /**
     * Add axios response interceptor
     */
    responseInterceptor() {
      HTTP.interceptors.response.use((response) => {
        // loading spinner -
        this.$store.commit('fm/messages/subtractLoading');

        // create notification, if find message text
        if (Object.prototype.hasOwnProperty.call(response.data, 'result')) {
          if (response.data.result.message) {
            // show notification
            EventBus.$emit('addNotification', response.data.result);

            // set action result
            this.$store.commit('fm/messages/setActionResult', response.data.result);
          }
        }

        return response;
      }, (error) => {
        // loading spinner -
        this.$store.commit('fm/messages/subtractLoading');

        // set error message
        this.$store.commit('fm/messages/setError', error);

        const errorMessage = {
          status: 'error',
          message: '',
        };

        // add message
        if (error.response) {
          errorMessage.message = error.response.data.message || error.response.statusText;
        } else if (error.request) {
          errorMessage.message = error.request.statusText || 'Network error';
        } else {
          errorMessage.message = error.message;
        }

        // show notification
        EventBus.$emit('addNotification', errorMessage);

        return Promise.reject(error);
      });
    },

    /**
     * Select manager (when shown 2 file manager windows)
     * @param managerName
     */
    selectManager(managerName) {
      if (this.activeManager !== managerName) {
        this.$store.commit('fm/setActiveManager', managerName);
      }
    },
  },
};
</script>

<style lang="scss">
  @import "~plyr/src/sass/plyr.scss";
  .md-finder {
    background: #f0f0f0;
    position: relative;
  }
  .fm {
    position: relative;
    height: 100%;
    padding: 1rem 1rem 0;
    background-color: white;

    &:-moz-full-screen {
      background-color: white;
    }

    &:-webkit-full-screen {
      background-color: rgb(53, 34, 34);
    }

    &:fullscreen {
      background-color: white;
    }

    .fm-body {
      display: flex;
      height: 100%;
      margin-right: -15px;
      margin-left: -15px;
      position: relative;
      padding-top: 1rem;
      padding-bottom: 1rem;
      border-top: 1px solid #6d757d;
      border-bottom: 1px solid #6d757d;
    }

    .unselectable {
      -webkit-touch-callout: none;
      -webkit-user-select: none;
      -moz-user-select: none;
      -ms-user-select: none;
      user-select: none;
    }
  }

  .fm-error {
    color: white;
    background-color: #dc3545;
    border-color: #dc3545;
  }

  .fm-danger {
    color: #dc3545;
    background-color: white;
    border-color: #dc3545;
  }

  .fm-warning {
    color: #ffc107;
    background-color: white;
    border-color: #ffc107;
  }

  .fm-success {
    color: #28a745;
    background-color: white;
    border-color: #28a745;
  }

  .fm-info {
    color: #17a2b8;
    background-color: white;
    border-color: #17a2b8;
  }

  .fm.fm-full-screen {
    width: 100%;
    height: 100%;
    padding-bottom: 0;
  }
</style>

