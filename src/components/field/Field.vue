<template>
    <div class="field" :class="[fieldType, newPosition, {
        'is-expanded': expanded,
        'is-grouped-multiline': groupMultiline
    }]">
        <label class="label" v-if="label">{{ label }}</label>
        <slot></slot>
        <p class="help" :class="newType" v-if="newMessage" v-html="formattedMessage"></p>
    </div>
</template>

<script>
    export default {
        name: 'bField',
        props: {
            type: String,
            label: String,
            message: [String, Array],
            grouped: Boolean,
            groupMultiline: Boolean,
            position: String,
            expanded: Boolean,
            addons: {
                type: Boolean,
                default: true
            }
        },
        data() {
            return {
                newType: this.type,
                newMessage: this.message,
                _isField: true // Used internally by Input and Select
            }
        },
        watch: {
            /**
             * Set internal type when prop change.
             */
            type(value) {
                this.newType = value
            },

            /**
             * Set internal message when prop change.
             */
            message(value) {
                this.newMessage = value
            }
        },
        computed: {
            /**
             * Correct Bulma class for the side of the addon or group.
             *
             * This is not kept like the others (is-small, etc.),
             * because since 'has-addons' is set automatically it
             * doesn't make sense to teach users what addons are exactly.
             */
            newPosition() {
                if (this.position === undefined) return

                const position = this.position.split('-')
                if (position.length < 1) return

                const prefix = this.grouped
                    ? 'is-grouped-'
                    : 'has-addons-'

                if (this.position) return prefix + position[1]
            },

            /**
             * Field has addons if there are more than one slot
             * (element / component) in the Field.
             * Or is grouped when prop is set.
             */

            fieldType() {
                if (this.grouped) {
                    return 'is-grouped'
                } else if (this.$slots.default !== undefined && this.$slots.default.length > 1 && this.addons) {
                    return 'has-addons'
                }
            },

            /**
             * Formatted message in case it's an array
             * (each element is separated by <br> tag)
             */
            formattedMessage() {
                if (this.newMessage) {
                    if (Array.isArray(this.newMessage)) {
                        return this.newMessage.filter((value) => {
                            if (value) {
                                return value
                            }
                        }).join(' <br> ')
                    } else {
                        return this.newMessage
                    }
                } else {
                    return this.newMessage
                }
            }
        }
    }
</script>
