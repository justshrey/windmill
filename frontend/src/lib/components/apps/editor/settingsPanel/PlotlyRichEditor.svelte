<script lang="ts">
	import type { RichConfiguration } from '../../types'
	import InputsSpecEditor from './InputsSpecEditor.svelte'
	import PanelSection from './common/PanelSection.svelte'

	export let datasets: RichConfiguration | undefined = undefined
	export let xData: RichConfiguration | undefined = undefined
	export let id: string
</script>

<PanelSection
	title={`Plotly configuration`}
	tooltip="The configuration is divided into two parts: X-axis data and an array of datasets. Each dataset hold the data fot he Y-axis and the configuration for the plot (type, color, etc)."
>
	<div class="w-full flex flex-col gap-4">
		{#if xData}
			<InputsSpecEditor
				key={`X-axis data`}
				bind:componentInput={xData}
				{id}
				userInputEnabled={false}
				shouldCapitalize={true}
				resourceOnly={false}
				fieldType={xData?.['fieldType']}
				subFieldType={xData?.['subFieldType']}
				format={xData?.['format']}
				selectOptions={xData?.['selectOptions']}
				tooltip={xData?.['tooltip']}
				fileUpload={xData?.['fileUpload']}
				placeholder={xData?.['placeholder']}
				customTitle={xData?.['customTitle']}
				displayType={false}
			/>
		{/if}

		{#if datasets}
			<InputsSpecEditor
				key={`Dataset`}
				bind:componentInput={datasets}
				{id}
				userInputEnabled={false}
				shouldCapitalize={true}
				resourceOnly={false}
				fieldType={datasets?.['fieldType']}
				subFieldType={datasets?.['subFieldType']}
				format={datasets?.['format']}
				selectOptions={datasets?.['selectOptions']}
				tooltip="For each dataset, you can specify the data for the Y-axis and the configuration for the plot (type, color, etc). If you want to have an eval for every data point, you can switch to JSON mode."
				fileUpload={datasets?.['fileUpload']}
				placeholder={datasets?.['placeholder']}
				customTitle={datasets?.['customTitle']}
				displayType={false}
				shouldFormatExpression={true}
				allowTypeChange={false}
			/>
		{/if}
	</div>
</PanelSection>
