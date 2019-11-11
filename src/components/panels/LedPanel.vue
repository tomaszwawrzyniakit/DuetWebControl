<style scoped>
.v-btn-toggle {
	display: flex;
}
.v-btn-toggle > button {
	display: flex;
	flex: 1 1 auto;
}
</style>

<template>
	<v-card v-show="canControlLeds">
		<v-card-title class="pb-0">
			<v-icon small class="mr-1">mdi-led-on</v-icon> {{ $t('panel.led.caption') }}
		</v-card-title>

		<v-card-text class="py-0">
			<v-row align="start">
				<v-col cols="12" sm="auto" order="0" order-sm="1" class="flex-sm-grow-1">
					<slider v-model="ledValue" :disabled="uiFrozen"></slider>
				</v-col>
			</v-row>
		</v-card-text>
	</v-card>
</template>

<script>
'use strict'

import { mapState, mapGetters, mapActions } from 'vuex'

export default {
	computed: {
		...mapState('settings', ['ledHeaterPin']),
		...mapGetters(['uiFrozen']),
		canControlLeds() {
			return !this.uiFrozen && this.ledHeaterPin>-1;
		},
		ledValue: {
			get() {
				if (this.canControlLeds) {
					return Math.round(this.currValue * 100) || 0;
				}
				return 0;
			},
			set(value) {
				value = Math.min(100, Math.max(0, value)) / 100;
				this.currValue = value;
				this.sendCode(`M42 P${this.ledHeaterPin} S${value.toFixed(2)}`);
			}
		}
	},
	data() {
		return {
			currValue: 0
		}
	},
	methods: {
		...mapActions('machine', ['sendCode'])
	},
}
</script>
