<script>
import { mapState, mapMutations } from 'vuex';
import Pusher from 'pusher-js';

export default {
    name: 'IO',

    data: () => ({
        echo: null,
        visible: false,
        imports: [],
        exports: [],
    }),

    computed: {
        ...mapState(['user', 'meta']),
        ...mapState('websockets', ['ioChannel']),
        ...mapState('layout', ['isTouch']),
        count() {
            return this.imports.length + this.exports.length;
        },
    },

    created() {
        this.connect();
        this.listen();
    },

    methods: {
        ...mapMutations('websockets', ['connect']),
        listen() {
            window.Echo.private(this.ioChannel)
                .listen('.io-started', ({ operation }) => {
                    this.push(operation);
                }).listen('.io-updated', ({ operation }) => {
                    this.update(operation);
                }).listen('.io-stopped', ({ operation }) => {
                    this.remove(operation);
                });
        },
        push(operation) {
            const index = this[this.type(operation.type)]
                .findIndex(op => op.id === operation.id);

            if (index === -1) {
                this[this.type(operation.type)].push(operation);
            }
        },
        update(operation) {
            const existing = this[this.type(operation.type)]
                .find(({ id }) => id === operation.id);

            if (existing) {
                existing.entries = operation.entries;
                existing.status = operation.status;
                return;
            }

            this.push(operation);
        },
        remove(operation) {
            const index = this[this.type(operation.type)]
                .findIndex(({ id }) => id === operation.id);

            if (index >= 0) {
                this[this.type(operation.type)].splice(index, 1);
            }
        },
        type(type) {
            return type === 1
                ? 'imports'
                : 'exports';
        },
        hide() {
            this.visible = false;
        },
        toggle() {
            this.visible = !this.visible;
        },
    },

    render() {
        return this.$scopedSlots.default({
            visible: this.visible,
            isTouch: this.isTouch,
            count: this.count,
            imports: this.imports,
            exports: this.exports,
            toggle: this.toggle,
            hide: this.hide,
        });
    },
};
</script>
