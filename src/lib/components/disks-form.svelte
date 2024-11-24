<script lang="ts">
	import { IconCircleXFilled, IconCopy, IconPlayerEjectFilled } from '@tabler/icons-svelte';
	import { flip } from 'svelte/animate';
	import { slide } from 'svelte/transition';

	export type Disk = {
		letter: string;
		totalSpaceGB: number;
		usedGB: number;
	};

	export type OperationInfo = {
		id: string;
		carNumber: string;
		surname: string;
		dateStr: string;
		diskLetter: string;
		progress: number;
		action: string;
	};

	type Props = {
		disks: Disk[];
		runningOperations: OperationInfo[];
		infoFilled?: boolean;
		onEject?: (disk: Disk) => any;
		onCancel?: (operation: OperationInfo) => any;
		onRun?: (disk: Disk) => any;
	};

	const { infoFilled, runningOperations, onEject, onCancel, disks, onRun }: Props = $props();
</script>

<ul class="flex flex-col gap-2">
	{#each disks as disk (disk.letter)}
		{@const operation = runningOperations.find((op) => op.diskLetter === disk.letter)}
		<a
			animate:flip
			transition:slide={{ axis: 'y', duration: 230 }}
			href={null}
			onclick={() => {
				if (operation) return;
				// @ts-ignore
				setDiskLetter?.(disk.letter === diskLetter ? '' : disk.letter);
			}}
			class={`group relative flex h-fit w-96 flex-row items-center justify-between gap-3 overflow-hidden rounded-lg border-2 border-base-content/20 p-3`}
		>
			<span class="rounded-btn bg-base-content/20 px-3 py-1 text-xl font-bold opacity-80"
				>{disk.letter}:</span
			>
			{#if operation}
				<div class="flex flex-1 flex-col gap-2">
					<div class="flex-1 text-center text-sm italic">
						{operation.action}
						{operation.surname} ({operation.carNumber}) [{operation.dateStr}]
					</div>
					<div class="relative h-8 flex-1 rounded-btn bg-base-content/20">
						<div
							style:width={`${operation.progress * 100}%`}
							class="striped h-full overflow-hidden rounded-btn transition-all"
						></div>
						<span
							class="absolute left-1/2 top-1/2 flex h-6 -translate-x-1/2 -translate-y-1/2 rounded-lg bg-base-content/50 px-2 py-1 text-xs text-base-300 opacity-85 backdrop-blur-sm"
							>{(operation.progress * 100).toFixed(2)} %</span
						>
					</div>
					<div class="h-0 w-full overflow-hidden transition-all group-hover:h-8">
						<button
							onclick={() => {
								onCancel?.(operation);
							}}
							class="btn btn-error btn-sm w-full"
						>
							<IconCircleXFilled />
							Отменить</button
						>
					</div>
				</div>
			{:else}
				<div class="flex flex-1 flex-col gap-2">
					<div class="relative h-8 flex-1 rounded-btn bg-base-content/20">
						<div
							style:width={`${(disk.usedGB / disk.totalSpaceGB) * 100}%`}
							class="h-full rounded-btn bg-primary transition-all"
						></div>
						<span
							class="absolute left-1/2 top-1/2 flex h-6 -translate-x-1/2 -translate-y-1/2 rounded-lg bg-base-content/50 px-2 py-1 text-xs text-base-300 opacity-85 backdrop-blur-sm"
							>{disk.usedGB} / {disk.totalSpaceGB} GB</span
						>
					</div>
					<div
						class="flex h-0 w-full justify-center gap-2 overflow-hidden transition-all group-hover:h-8"
					>
						<button
							disabled={!infoFilled}
							onclick={() => {
								onRun?.(disk);
							}}
							class="btn btn-success btn-sm justify-between"
						>
							Перенести записи
							<IconCopy size={16} />
						</button>
						<button
							onclick={() => {
								onEject?.(disk);
							}}
							class="btn btn-warning btn-sm justify-between"
						>
							Извлечь
							<IconPlayerEjectFilled size={16} />
						</button>
					</div>
				</div>
			{/if}
		</a>
	{/each}
</ul>

<style>
	.striped {
		background-image: repeating-linear-gradient(
			-45deg,
			oklch(var(--su) / 0.5),
			oklch(var(--su) / 0.5) 1rem,
			oklch(var(--su)) 1rem,
			oklch(var(--su)) 2rem
		);

		background-size: 200% 200%;
		animation: barberpole 10s linear infinite;
	}

	@keyframes barberpole {
		100% {
			background-position: 100% 100%;
		}
	}
</style>
