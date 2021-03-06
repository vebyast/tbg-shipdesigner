<template>
	<tr class="principal-frame-raw" v-bind:class="{ 'has-error': !is_valid_frame }">
		<td class="name-column" colspan="2">Size: {{frame_size}}</td>

		<td v-if="!se_design.refit_valid" :class="{
				'part-column': true,
				'compare-base-value': se_design.compare_base && se_design.principal_frame !== se_design.compare_base.principal_frame,
			}">
			<select v-model="principal_frame" class="part-column-select">
				<option
					v-for="princ_frame_value in se_design.valid_frames"
					:key="princ_frame_value['Name']"
					:class="{ 'compare-base-value': se_design.compare_base && princ_frame_value['Name'] === se_design.compare_base.principal_frame }">
					{{princ_frame_value['Name']}}
				</option>
				<option v-if="!is_valid_frame" class="has-error">{{principal_frame}}</option>
			</select>
		</td>
		<td v-else>{{principal_frame}}</td>

		<template v-for="name in stats_raw.names">
			<StatlineCell :key="name" :stats="stats_raw" :name="name"></StatlineCell>
		</template>
		
		<td class="weight-internal-column" :class="weight_summary_class">{{se_design.weight_internal.toFixed(2)}}</td>
		<td class="weight-external-column">{{se_design.weight_external.toFixed(2)}}</td>

		<td class="br-column">{{se_design.cost_BR_raw.toFixed(2)}}</td>
		<td class="sr-column">{{se_design.cost_SR_raw.toFixed(2)}}</td>

		<template v-if="se_design.refit_valid">
			<td class="br-column">{{se_design.refit_cost_BR_raw.toFixed(2)}}</td>
			<td class="sr-column">{{se_design.refit_cost_SR_raw.toFixed(2)}}</td>
		</template>

		<td class="power-cost-column" :title="power_final_title" :class="power_final_class">{{se_design.cost_power_raw.toFixed(2)}}</td>
		<td class="power-gen-column" :title="power_final_title" :class="power_final_class">{{se_design.power_generation_raw.toFixed(2)}}</td>

		<template v-for="name in crew_raw.names">
			<StatlineCell :key="name" :stats="crew_raw" :name="name"></StatlineCell>
		</template>

		<td class="build-time-column">{{build_time_frame}}</td>

		<td class="tech-year-column">{{tech_year_frame}} (Frame)</td>
	</tr>
</template>


<script>

import { mapGetters } from 'vuex';

import StatlineCell from '@/components/shipdesigner/statline-cell.vue';

import { frac } from '@/lib/ui-functions.js';

export default {
	name: 'PrincipalFrameRaw',
	components: {
		StatlineCell,
	},
	computed: {
		is_valid_frame() {
			return this.se_design.is_valid_frame;
		},
		power_final_title() {
			if (this.has_power_error) {
				return 'Error: Power cost greater than power generation.';
			} else {
				return '';
			}
		},
		power_final_class() {
			return {
				'has-error': this.has_power_error,
			};
		},
		has_power_error() {
			return !this.se_design.omit_validation && this.se_design.cost_power_raw > this.se_design.power_generation_raw;
		},
		weight_summary_class() {
			return {
				'has-error': this.has_weight_error,
			};
		},
		has_weight_error() {
			return !this.se_design.omit_validation && this.se_design.weight_internal > this.se_design.frame_max_size_raw;
		},
		principal_frame: {
			get() {
				return this.se_design.principal_frame;
			},
			set(value) {
				this.se_design.principal_frame = value;
			},
		},
		stats_raw() {
			return this.se_design.stats_raw;
		},
		crew_raw() {
			return this.se_design.cost_crew_raw;
		},
		build_time_frame() {
			return frac(this.se_design.build_time_frame, 12);
		},
		tech_year_frame() {
			return this.se_design.tech_year_frame;
		},
		frame_size() {
			return this.se_design.frame_size.toFixed(2);
		},
		...mapGetters([
			'se_design',
		]),
	},
	methods: {
	},
};
</script>


<style>
</style>

<style scoped>
.principal-frame-raw {
	background: #111;
	color: #fff;

	width: 100%;
	margin: 0px;

	/* position: relative; */
	/* left: 2px; */
	/* top: 2px; */
}

.name-column {
	text-align: right;
	padding-right: 0.2em;
}

/* override global value for this one */
.stat-column {
	text-align: center;
}

.weight-internal-column {
	text-align: center;
}

.weight-external-column {
	text-align: center;
}

.br-column {
	text-align: center;
}

.sr-column {
	text-align: center;
}

.power-gen-column {
	text-align: center;
}

.power-cost-column {
	text-align: center;
}

.compare-base-value {
	background: #aa80ff;
}

.has-error {
	background: #d60000;
}
</style>
