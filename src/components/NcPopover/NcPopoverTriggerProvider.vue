<!--
  - SPDX-FileCopyrightText: 2024 Nextcloud GmbH and Nextcloud contributors
  - SPDX-License-Identifier: AGPL-3.0-or-later
-->

<script>
import { defineComponent } from 'vue'

export default defineComponent({
	name: 'NcPopoverTriggerProvider',

	provide() {
		return {
			'NcPopover:trigger:shown': () => this.shown,
			'NcPopover:trigger:attrs': () => this.triggerAttrs,
		}
	},

	props: {
		shown: {
			type: Boolean,
			required: true,
		},
		popupRole: {
			type: String,
			default: undefined,
		},
	},

	computed: {
		triggerAttrs() {
			return {
				'aria-haspopup': this.popupRole,
				'aria-expanded': this.shown.toString(),
			}
		},
	},

	render() {
		return this.$slots.default?.({
			attrs: this.triggerAttrs,
		})
	},
})
</script>
