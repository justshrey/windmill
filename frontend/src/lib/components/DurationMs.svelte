<script lang="ts">
	import { msToSec } from '$lib/utils'
	import { faHourglassHalf } from '@fortawesome/free-solid-svg-icons'
	import { Icon } from 'svelte-awesome'
	import { Badge } from './common'
	import Tooltip from './Tooltip.svelte'

	export let duration_ms: number
</script>

<Badge large>
	<Icon data={faHourglassHalf} scale={0.7} class="mr-2" />
	Ran in {msToSec(duration_ms)}s<Tooltip
		>For scripts, this is the time that it took to execute from the time it was picked by a worker,
		to the time the result was stored.<br />
		For flows, it is the cumulative time of the execution of each steps. Suspend/sleep/transition times
		are not accounted for, and each step's duration in a parallel branch would be added. Hence the time
		here can differ from the time it took for the flow to have a result from start.</Tooltip
	>
</Badge>
