<script lang="ts">
	import DisksForm, { type OperationInfo } from '$lib/components/disks-form.svelte';
	import InfoForm from '$lib/components/info-form.svelte';
	import Modal from '$lib/components/UI/modal.svelte';
	import { IconPlayerPlayFilled } from '@tabler/icons-svelte';

	const operationInfo = $state({
		carNumber: '',
		surname: '',
		dateStr: '',
		diskLetter: ''
	});

	const runningOperations = $state<OperationInfo[]>([
		{
			id: crypto.randomUUID(),
			diskLetter: 'G',
			progress: 0,
			surname: 'Ivanov',
			carNumber: '999',
			dateStr: '23.11.2024',
			action: 'Копирование'
		}
	]);
</script>

<Modal></Modal>
<div class="flex flex-col items-center gap-5">
	<div class="flex gap-5">
		<div class="flex flex-col gap-3 p-3">
			<span class="font-semibold">Информация о записях</span>
			<InfoForm info={operationInfo} />
		</div>
		<div class="flex flex-col gap-3 p-3">
			<span class="font-semibold">Флеш-накопители</span>
			<DisksForm
				{runningOperations}
				diskLetter={operationInfo.diskLetter}
				setDiskLetter={(letter) => (operationInfo.diskLetter = letter)}
			/>
		</div>
	</div>
	<div class="flex flex-col items-center gap-1.5">
		<button class="btn btn-success w-96"
			>Перенести на ПК
			<IconPlayerPlayFilled />
		</button>
		<span class="text-xs opacity-70"
			>Диск {operationInfo.diskLetter}: ({operationInfo.carNumber}) {operationInfo.surname} [{operationInfo.dateStr}]</span
		>
	</div>
</div>
