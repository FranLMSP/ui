<template>
    <div class="box has-background-light raises-on-hover file-box has-padding-large">
        <figure class="image is-32x32 avatar">
            <img class="is-rounded"
                :src="route('core.avatars.show', file.owner.avatarId)">
        </figure>
        <p class="has-text-centered has-margin-bottom-medium">
            <fa :icon="icon"
                size="3x"/>
        </p>
        <h5 class="title is-5 filename has-text-centered"
            v-tooltip="file.name">
            {{ file.name }}
        </h5>
        <p class="has-text-centered"
           v-tooltip="date(file.createdAt)">
            <span class="icon is-small">
                <fa icon="calendar-alt"/>
            </span>
            {{ timeFromNow(file.createdAt) }}
        </p>
        <p class="has-margin-top-small has-text-centered">
            <span class="icon is-small">
                <fa icon="database"/>
            </span>
            {{ file.size / 1000 | numberFormat }} KB
        </p>
        <div class="has-text-centered has-margin-top-medium">
            <div class="details">
                <a class="button is-naked"
                    v-if="canAccess('core.files.link')"
                    @click.stop="link">
                    <span class="icon">
                        <fa icon="link"/>
                    </span>
                </a>
                <a class="button is-naked"
                    @click.stop="show"
                    v-if="isViewable">
                    <span class="icon">
                        <fa icon="eye"/>
                    </span>
                </a>
                <a class="button is-naked"
                    :href="route('core.files.download', file.id)">
                    <span class="icon">
                        <fa icon="cloud-download-alt"/>
                    </span>
                </a>
                <confirmation placement="top"
                    @confirm="$emit('delete')"
                    v-if="file.isDeletable && canAccess('core.files.destroy')">
                    <a class="button is-naked">
                        <span class="icon">
                            <fa icon="trash-alt"/>
                        </span>
                    </a>
                </confirmation>
            </div>
        </div>

        <url :show="temporaryLink !== ''"
            :link="temporaryLink"
            @close="temporaryLink = ''"/>
    </div>
</template>

<script>
import { library } from '@fortawesome/fontawesome-svg-core';
import {
    faEye, faCloudDownloadAlt, faTrashAlt, faLink, faCalendarAlt, faDatabase,
} from '@fortawesome/free-solid-svg-icons';
import { VTooltip } from 'v-tooltip';
import { Confirmation } from '@enso-ui/bulma';
import formatDistance from '@core-modules/plugins/date-fns/formatDistance';
import format from '@core-modules/plugins/date-fns/format';
import { files } from '@enso-ui/mixins';
import Url from './Url.vue';

library.add(faEye, faCloudDownloadAlt, faTrashAlt, faLink, faCalendarAlt, faDatabase);

export default {
    name: 'File',

    inject: ['canAccess', 'errorHandler'],

    directives: { tooltip: VTooltip },

    components: { Confirmation, Url },

    mixins: [files],

    props: {
        file: {
            type: Object,
            required: true,
        },
    },

    data: () => ({
        temporaryLink: '',
    }),

    methods: {
        link() {
            axios.get(route('core.files.link', this.file.id))
                .then(({ data }) => (this.temporaryLink = data.link))
                .catch(this.errorHandler);
        },
        show() {
            window.open(route('core.files.show', this.file.id), '_blank').focus();
        },
        timeFromNow(date) {
            return formatDistance(date);
        },
        date(date) {
            return format(date);
        },
    },
};
</script>

<style lang="scss" scoped>
    .file-box {
        position: relative;
        cursor: pointer;

        .avatar {
            position: absolute;
            top: .6em;
            left: .6em;
        }

        .details {
            display: flex;
            justify-content: center;
        }

        .title.filename {
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
    }
</style>
