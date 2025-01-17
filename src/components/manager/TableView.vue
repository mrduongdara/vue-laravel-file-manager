<template>
    <div class="fm-table">
        <table class="table table-sm">
            <thead>
                <tr>
                    <th class="w-65" v-on:click="sortBy('name')">
                        {{ lang.manager.table.name }}
                        <template v-if="sortSettings.field === 'name'">
                            <i class="material-icons"
                               v-show="sortSettings.direction === 'down'">arrow_drop_down</i>
                            <i class="material-icons"
                               v-show="sortSettings.direction === 'up'">arrow_drop_up</i>
                        </template>
                    </th>
                    <th class="w-10" v-on:click="sortBy('size')">
                        {{ lang.manager.table.size }}
                        <template v-if="sortSettings.field === 'size'">
                            <i class="material-icons"
                               v-show="sortSettings.direction === 'down'">arrow_drop_down</i>
                            <i class="material-icons"
                               v-show="sortSettings.direction === 'up'">arrow_drop_up</i>
                        </template>
                    </th>
                    <th class="w-10" v-on:click="sortBy('type')">
                        {{ lang.manager.table.type }}
                        <template v-if="sortSettings.field === 'type'">
                            <i class="material-icons"
                               v-show="sortSettings.direction === 'down'">arrow_drop_down</i>
                            <i class="material-icons"
                               v-show="sortSettings.direction === 'up'">arrow_drop_up</i>
                        </template>
                    </th>
                    <th v-on:click="sortBy('date')">
                        {{ lang.manager.table.date }}
                        <template v-if="sortSettings.field === 'date'">
                            <i class="material-icons"
                               v-show="sortSettings.direction === 'down'">arrow_drop_down</i>
                            <i class="material-icons"
                               v-show="sortSettings.direction === 'up'">arrow_drop_up</i>
                        </template>
                    </th>
                </tr>
            </thead>
            <tbody>
                <tr v-if="!isRootPath">
                    <td colspan="4" class="fm-content-item" v-on:click="levelUp">
                        <i class="material-icons">trending_down</i>
                    </td>
                </tr>
                <tr v-for="(directory, index) in directories"
                    v-bind:key="`d-${index}`"
                    v-bind:class="{'table-info': checkSelect('directories', directory.path)}"
                    v-on:click="selectItem('directories', directory.path, $event)"
                    v-on:contextmenu.prevent="contextMenu(directory, $event)">
                    <td class="fm-content-item unselectable"
                        v-bind:class="(acl && directory.acl === 0) ? 'text-hidden' : ''"
                        v-on:dblclick="selectDirectory(directory.path)">
                        <span class="folder-item"><v-icon>folder</v-icon> {{ directory.basename }}</span>
                    </td>
                    <td></td>
                    <td>{{ lang.manager.table.folder }}</td>
                    <td>
                        {{ timestampToDate(directory.timestamp) }}
                    </td>
                </tr>
                <tr v-for="(file, index) in files"
                    v-bind:key="`f-${index}`"
                    v-bind:class="{'table-info': checkSelect('files', file.path)}"
                    v-on:click="selectItem('files', file.path, $event)"
                    v-on:dblclick="selectAction(file.path, file.extension)"
                    v-on:contextmenu.prevent="contextMenu(file, $event)">
                    <td class="fm-content-item unselectable"
                        v-bind:class="(acl && file.acl === 0) ? 'text-hidden' : ''">
                        <i class="material-icons"
                           v-bind:class="extensionToIcon(file.extension)"></i>
                        {{ file.filename ? file.filename : file.basename }}
                    </td>
                    <td>{{ bytesToHuman(file.size) }}</td>
                    <td>
                        {{ file.extension }}
                    </td>
                    <td>
                        {{ timestampToDate(file.timestamp) }}
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import translate from './../../mixins/translate';
import helper from './../../mixins/helper';
import managerHelper from './mixins/manager';

export default {
  name: 'table-view',
  mixins: [translate, helper, managerHelper],
  props: {
    manager: { type: String, required: true },
  },
  computed: {
    /**
     * Sort settings
     * @returns {*}
     */
    sortSettings() {
      return this.$store.state.fm[this.manager].sort;
    },
  },
  methods: {
    /**
     * Sort by field
     * @param field
     */
    sortBy(field) {
      this.$store.dispatch(`fm/${this.manager}/sortBy`, { field, direction: null });
    },
  },
};
</script>

<style lang="scss">
    .fm-table {
        width: 100%;
        background: #ffffff;
        
        thead th{
            background: white;
            position: sticky;
            top: 0;
            z-index: 10;
            cursor: pointer;
            border-top: none;
            text-align: left;

            &:hover {
                background-color: #f8f9fa;
            }

            & > i {
                padding-left: 0.5rem;
            }
        }

        td {
            white-space: nowrap;
            overflow: hidden;
            border-bottom: 1px solid #f7f7f7;
        }

        tr:hover {
            background-color: #f8f9fa;
        }

        .w-10 {
            width: 10%;
        }

        .w-65 {
            width: 65%;
        }

        .fm-content-item {
            cursor: pointer;
        }

        .text-hidden {
            color: #cdcdcd;
        }
        .folder-item {
            line-height: 1.8;
            vertical-align: bottom;
        }
    }
</style>
