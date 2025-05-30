<!--
 - SPDX-FileCopyrightText: 2024 Nextcloud GmbH and Nextcloud contributors
 - SPDX-License-Identifier: AGPL-3.0-or-later
-->
```ts static
import {
	spawnDialog,
} from '@nextcloud/vue/functions/dialog'
```

## Definitions

```ts static
type SpawnDialogOptions = {
	/**
	 * Container to mount the dialog to
	 * @default document.body
	 */
	container?: Element | string
}

/**
 * Spawn a single-use Vue dialog instance to get the result when it is closed
 *
 * @param dialog - Dialog component to spawn
 * @param props - Props to pass to the dialog instance
 * @param options - Spawning options
 */
declare function spawnDialog(
	dialog: Component,
	props: object = {},
	optionsOrOnClose: SpawnDialogOptions = {},
): Promise<unknown>
```

## Usage

The main use case is to be able to spawn a dialog from code, without the need of including the dialog component in the template.
So a Vue dialog can be spawned in any context and not just from Vue components but also from callback functions or other API.

```vue
<template>
	<div>
		<NcButton @click="onSpawnDialog">🖱 click to spawn a dialog</NcButton>
	</div>
</template>

<script>
// Example dialog component that would be normally imported
// Important: The dialog must emit a 'close' event whenever it is closed
const ExampleDialog = {
	template: `
		<NcDialog :buttons="buttons"
			name="Spawned dialog"
			message="This dialog was spawned using the 'spawnDialog' function."
			@closing="saveResult"
			@update:open="onClose" />`,
	components: {
		NcDialog,
	},
	props: {
		timesClicked: {
			type: Number,
			required: true,
		},
	},
	data() {
		return {
			result: null,
		}
	},
	computed: {
		buttons() {
			return [
				{
					label: 'Cancel',
					callback: () => 'cancelled',
				},
				{
					label: 'Accept',
					variant: 'primary',
					callback: () => 'accepted',
				},
			]
		}
	},
	methods: {
		saveResult(result) {
			this.result = result
		},
		onClose() {
			this.$emit('close', this.result)
		},
	}
}

export default {
	data() {
		return {
			timesClicked: 0,
		}
	},
	methods: {
		async onSpawnDialog() {
			this.timesClicked += 1
			const result = await spawnDialog(ExampleDialog, { timesClicked: this.timesClicked })
			window.alert(`Dialog was ${result}`)
		},
	},
}
</script>
```