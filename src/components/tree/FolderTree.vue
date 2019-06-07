<template>
    <div class="fm-tree">
        <div class="fm-tree-disk sticky-top">
          <v-btn outline disabled>
            {{ selectedDisk }} <v-icon right>storage</v-icon>
          </v-btn>
        </div>
        <branch v-bind:parent-id="0"></branch>
        <v-spacer></v-spacer>
    </div>
</template>

<script>
import Branch from './Branch.vue';

export default {
  name: 'FolderTree',
  components: {
    branch: Branch,
  },
  computed: {
    /**
     * Selected Disk
     * @returns {*}
     */
    selectedDisk() {
      return this.$store.getters['fm/selectedDisk'];
    },
  },
};
</script>

<style lang="scss">
    
</style>
