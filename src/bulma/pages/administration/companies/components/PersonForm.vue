<template>
    <modal show
        portal="person-form"
        v-on="$listeners">
        <enso-form class="box has-background-light"
            v-bind="$attrs"
            disable-state
            v-on="$listeners"
            ref="form">
            <template v-slot:actions>
                <a class="button is-warning"
                    @click="editPerson">
                    <span class="is-hidden-mobile">
                        {{ i18n('Edit Person') }}
                    </span>
                    <span class="icon">
                        <fa icon="user-tie"/>
                    </span>
                    <span class="is-hidden-mobile"/>
                </a>
            </template>
        </enso-form>
    </modal>
</template>

<script>
import { Modal } from '@enso-ui/bulma';
import { EnsoForm } from '@enso-ui/forms/bulma';

export default {
    name: 'PersonForm',

    components: { Modal, EnsoForm },

    inject: ['i18n'],

    props: {
        companyId: {
            type: Number,
            required: true,
        },
    },

    methods: {
        editPerson() {
            if (!this.field('id').value) {
                return;
            }

            this.$emit('edit-person', this.field('id').value);
        },
        field(field) {
            return this.$refs.form.data
                ? this.$refs.form.field(field)
                : null;
        },
    },
};
</script>

<style lang="scss">
    .person-form .modal-content {
        overflow: unset;
    }

    @media screen and (max-width: 1023px) {
        .person-form .modal-content {
            width: 95%;
        }
    }
</style>
