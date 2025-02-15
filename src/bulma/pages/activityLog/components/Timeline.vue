<template>
    <div class="box has-background-light">
        <div class="is-clearfix"/>
        <h4 class="title is-4 has-text-centered"
            v-if="!feed.length">
            {{ i18n('No activity found') }}
        </h4>
        <div :class="['timeline animated fadeIn', {'is-centered':!isTouch}]"
            v-for="(day, index) in feed"
            :key="index"
            v-else>
            <header class="timeline-header title is-4">
                {{ formatDate(day.date) }}
            </header>
            <div class="timeline-item"
                v-for="event in day.list"
                :key="event.id">
                <div :class="[
                        'timeline-marker is-icon',
                        { 'is-success': event.action.type === 1 },
                        { 'is-warning': event.action.type === 2 },
                        { 'is-danger': event.action.type === 3 },
                        { 'is-info': event.action.type === 4 },
                    ]">
                    <span class="icon is-small has-text-white">
                        <fa :icon="icon(event.action)"
                            size="xs"/>
                    </span>
                </div>
                <div class="timeline-content">
                    <event :event="event"/>
                </div>
            </div>
        </div>
        <div class="has-text-centered"
            v-if="feed.length">
            <button :class="['button', {'is-loading': loading}]"
                @click="$emit('load-more')">
                {{ i18n('Load more') }}
            </button>
        </div>
    </div>
</template>

<script>
import { mapState } from 'vuex';
import { library } from '@fortawesome/fontawesome-svg-core';
import {
    faSyncAlt, faPlus, faPencilAlt, faTrashAlt, faFlag,
} from '@fortawesome/free-solid-svg-icons';
import format from '@core-modules/plugins/date-fns/format';
import Event from './Event.vue';

library.add(faSyncAlt, faPlus, faPencilAlt, faTrashAlt, faFlag);

export default {
    name: 'Timeline',

    components: { Event },

    inject: ['i18n'],

    props: {
        feed: {
            type: Array,
            required: true,
        },
        loading: {
            type: Boolean,
            required: true,
        },
    },

    computed: {
        ...mapState('layout', ['isTouch']),
        days() {
            return this.feed.reduce();
        },
    },

    methods: {
        formatDate(date) {
            return format(date, 'l, F d');
        },
        icon({ icon, type }) {
            if (icon) {
                return icon;
            }

            switch (type) {
            case 1:
                return 'plus';
            case 2:
                return 'pencil-alt';
            case 3:
                return 'trash-alt';
            case 4:
                return 'flag';
            default:
                return 'exclamation';
            }
        },
    },
};
</script>

<style lang="scss" scoped>
    .timeline {
        .timeline-header {
            max-width: 12em;
            width: unset;
        }

        .timeline-item .timeline-content {
            align-items: flex-start;
        }
    }
</style>
