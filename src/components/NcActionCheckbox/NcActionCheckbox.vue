<!--
  - SPDX-FileCopyrightText: 2019 Nextcloud GmbH and Nextcloud contributors
  - SPDX-License-Identifier: AGPL-3.0-or-later
-->

<docs>
This component is made to be used inside of the [NcActions](#NcActions) component slots.

```vue
	<NcActions>
		<NcActionCheckbox @change="alert('(un)checked !')">First choice</NcActionCheckbox>
		<NcActionCheckbox value="second" @change="alert('(un)checked !')">Second choice</NcActionCheckbox>
		<NcActionCheckbox :model-value="true" @change="alert('(un)checked !')">Third choice (checked)</NcActionCheckbox>
		<NcActionCheckbox :disabled="true" @change="alert('(un)checked !')">Second choice (disabled)</NcActionCheckbox>
	</NcActions>
```
</docs>

<template>
	<li class="action" :class="{ 'action--disabled': disabled }" :role="isInSemanticMenu && 'presentation'">
		<span class="action-checkbox" :role="isInSemanticMenu && 'menuitemcheckbox'" :aria-checked="ariaChecked">
			<input :id="id"
				ref="checkbox"
				:disabled="disabled"
				:checked="modelValue"
				:value="value"
				:class="{ focusable: isFocusable }"
				type="checkbox"
				class="checkbox action-checkbox__checkbox"
				@keydown.enter.exact.prevent="checkInput"
				@change="onChange">
			<label ref="label" :for="id" class="action-checkbox__label">{{ text }}</label>

			<!-- fake slot to gather inner text -->
			<slot v-if="false" />
		</span>
	</li>
</template>

<script>
import ActionGlobalMixin from '../../mixins/actionGlobal.js'
import { createElementId } from '../../utils/createElementId.ts'
import { NC_ACTIONS_IS_SEMANTIC_MENU } from '../NcActions/useNcActions.ts'

export default {
	name: 'NcActionCheckbox',

	mixins: [ActionGlobalMixin],

	inject: {
		isInSemanticMenu: {
			from: NC_ACTIONS_IS_SEMANTIC_MENU,
			default: false,
		},
	},

	props: {
		/**
		 * id attribute of the checkbox element
		 */
		id: {
			type: String,
			default: () => 'action-' + createElementId(),
			validator: id => id.trim() !== '',
		},

		/**
		 * checked state of the the checkbox element
		 */
		modelValue: {
			type: Boolean,
			default: false,
		},

		/**
		 * value of the checkbox input
		 */
		value: {
			type: [String, Number],
			default: '',
		},

		/**
		 * disabled state of the checkbox element
		 */
		disabled: {
			type: Boolean,
			default: false,
		},
	},

	emits: [
		'change',
		'check',
		'uncheck',
		'update:modelValue',
	],

	computed: {
		/**
		 * determines if the action is focusable
		 *
		 * @return {boolean} is the action focusable ?
		 */
		isFocusable() {
			return !this.disabled
		},

		/**
		 * aria-checked attribute for role="menuitemcheckbox"
		 *
		 * @return {'true'|'false'|undefined} aria-checked value if needed
		 */
		ariaChecked() {
			if (this.isInSemanticMenu) {
				return this.modelValue ? 'true' : 'false'
			}
			return undefined
		},
	},

	methods: {
		checkInput(/* event */) {
			// by clicking we also trigger the change event
			this.$refs.label.click()
		},
		onChange(event) {
			/**
			 * Emitted when the checkbox state is changed
			 *
			 * @type {boolean}
			 */
			this.$emit('update:modelValue', this.$refs.checkbox.checked)

			/**
			 * Emitted when the checkbox state is changed
			 *
			 * @type {Event}
			 */
			this.$emit('change', event)

			if (this.$refs.checkbox.checked) {
				/**
				 * Emitted when the checkbox is checked
				 *
				 * @type {Event}
				 */
				this.$emit('check')
			} else {
				/**
				 * Emitted when the checkbox is unchecked
				 *
				 * @type {Event}
				 */
				this.$emit('uncheck')
			}
		},
	},
}
</script>

<style lang="scss" scoped>
@use '../../assets/action.scss' as *;
@include action-active;
@include action--disabled;

.action-checkbox {
	display: flex;
	align-items: flex-start;

	width: 100%;
	height: auto;
	margin: 0;
	padding: 0;

	cursor: pointer;
	white-space: nowrap;

	color: var(--color-main-text);
	border: 0;
	border-radius: 0; // otherwise Safari will cut the border-radius area
	background-color: transparent;
	box-shadow: none;

	font-weight: normal;
	line-height: var(--default-clickable-area);

	/* checkbox/radio fixes */
	&__checkbox {
		position: absolute;
		inset-inline-start: 0 !important;
		z-index: -1;
		opacity: 0;
	}

	&__label {
		display: flex;
		align-items: center; // align checkbox to text

		width: 100%;
		padding: 0 !important;
		padding-inline-end: $icon-margin !important;

		&::before {
			margin-block: 0 !important;
			margin-inline: calc((var(--default-clickable-area) - 14px) / 2) !important;
		}
	}

	&--disabled {
		&,
		.action-checkbox__label {
			cursor: pointer;
		}
	}
}

</style>
