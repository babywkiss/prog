<script lang="ts">
	import DisksForm, { type Disk, type OperationInfo } from '$lib/components/disks-form.svelte';
	import InfoForm from '$lib/components/info-form.svelte';
	import Modal from '$lib/components/UI/modal.svelte';
	import { IconInfoCircleFilled, IconUsb } from '@tabler/icons-svelte';
	// import { source } from 'sveltekit-sse';

	type OperationInput = {
		carNumber: string;
		surname: string;
		dateStr: string;
	};

	// LOGIC
	// const value = source('/events').select('message');
	const ejectDisk = async (letter: string) => {
		const index = disks.findIndex((d) => d.letter === letter);
		if (index !== -1) disks.splice(index, 1);
	};
	const cancelOperation = async (id: string) => {
		const index = runningOperations.findIndex((op) => op.id === id);
		if (index !== -1) runningOperations.splice(index, 1);
	};
	const runOperation = async (info: OperationInput, diskLetter: string) => {
		runningOperations.push({
			...info,
			diskLetter,
			id: crypto.randomUUID(),
			progress: 0,
			action: 'Копирование'
		});
	};
	// LOGIC

	// EXTERNAL STATE
	const disks = $state<Disk[]>([
		{ letter: 'G', totalSpaceGB: 64, usedGB: 45.7 },
		{ letter: 'K', totalSpaceGB: 64, usedGB: 22.3 }
	]);

	const runningOperations = $state<OperationInfo[]>([]);

	$effect(() => {
		const id = setInterval(() => {
			if (Math.random() > 0.5) {
				for (const [i, operation] of runningOperations.entries()) {
					if (Math.random() > 0.5) {
						if (operation.progress < 1) {
							operation.progress += 0.01;
						} else {
							runningOperations.splice(i, 1);
							break;
						}
					}
				}
			}
		}, 100);
		return () => {
			clearInterval(id);
		};
	});
	// EXTERNAL STATE

	const operationInfo = $state({
		carNumber: '',
		surname: '',
		dateStr: ''
	});

	const allFilled = $derived(!Object.values(operationInfo).some((v) => v.length === 0));
	let ejecting = $state<Disk>();
	let running = $state<Disk>();
	let cancelling = $state<OperationInfo>();
</script>

{#if running && allFilled}
	<Modal
		onClose={() => (running = undefined)}
		class="flex flex-col gap-5 rounded-lg bg-base-100 p-5"
	>
		<h3 class="font-bold">Вы действительно хотите перенести записи ?</h3>
		<div class="flex flex-col gap-1">
			<span>Данные будут перенесены с диска {running.letter}</span>
			<span>Номер авто: {operationInfo.carNumber}</span>
			<span>Фамилия : {operationInfo.surname}</span>
			<span>Дата : {operationInfo.dateStr}</span>
		</div>

		<div class="flex w-full justify-end gap-3">
			<button onclick={() => (running = undefined)} class="btn btn-neutral btn-sm"
				>Вернуться назад</button
			>
			<button
				onclick={async () => {
					if (!running || !allFilled) return;
					await runOperation(operationInfo, running.letter);
					Object.assign(operationInfo, {
						carNumber: '',
						surname: ''
					});
					running = undefined;
				}}
				class="btn btn-success btn-sm">Перенести</button
			>
		</div>
	</Modal>
{/if}

{#if cancelling}
	<Modal
		onClose={() => (cancelling = undefined)}
		class="flex flex-col gap-5 rounded-lg bg-base-100 p-5"
	>
		<h3 class="font-bold">Вы действительно хотите отменить операцию ?</h3>
		<div class="flex w-full justify-end gap-3">
			<button onclick={() => (cancelling = undefined)} class="btn btn-neutral btn-sm"
				>Вернуться назад</button
			>
			<button
				onclick={async () => {
					if (!cancelling) return;
					await cancelOperation(cancelling.id);
					cancelling = undefined;
				}}
				class="btn btn-error btn-sm">Да</button
			>
		</div>
	</Modal>
{/if}
{#if ejecting}
	<Modal
		onClose={() => (ejecting = undefined)}
		class="flex flex-col gap-5 rounded-lg bg-base-100 p-5"
	>
		<h3 class="font-bold">Вы действительно хотите извлечь диск {ejecting.letter} ?</h3>
		<div class="flex w-full justify-end gap-3">
			<button onclick={() => (ejecting = undefined)} class="btn btn-neutral btn-sm"
				>Вернуться назад</button
			>
			<button
				onclick={async () => {
					if (!ejecting) return;
					await ejectDisk(ejecting.letter);
					ejecting = undefined;
				}}
				class="btn btn-warning btn-sm">Извлечь</button
			>
		</div>
	</Modal>
{/if}
<div class="flex flex-col items-center gap-5">
	<div class="flex gap-5">
		<div class="flex flex-col gap-5 p-3">
			<span class="font-semibold">
				<IconInfoCircleFilled size={20} class="inline" />
				Информация о записях
			</span>
			<InfoForm info={operationInfo} />
		</div>
		<div class="flex flex-col gap-5 p-3">
			<span class="font-semibold">
				<IconUsb size={20} class="inline" />
				Флеш-накопители
			</span>
			<DisksForm
				{disks}
				{runningOperations}
				infoFilled={allFilled}
				onCancel={(op) => (cancelling = op)}
				onEject={(disk) => (ejecting = disk)}
				onRun={(disk) => (running = disk)}
			/>
		</div>
	</div>
	<div class="flex flex-col items-center gap-1.5"></div>
</div>
