<script lang="ts">
	import { IconCircleXFilled } from '@tabler/icons-svelte';

	type Disk = {
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

	const disks = $state<Disk[]>([
		{ letter: 'G', totalSpaceGB: 64, usedGB: 45.7 },
		{ letter: 'K', totalSpaceGB: 64, usedGB: 22.3 }
	]);

	type Props = {
		runningOperations: OperationInfo[];
		diskLetter?: string;
		setDiskLetter?: (value: string) => any;
	};

	const { diskLetter, setDiskLetter, runningOperations }: Props = $props();
</script>

<ul class="flex flex-col gap-2">
	{#each disks as disk}
		{@const operation = runningOperations.find((op) => op.diskLetter === disk.letter)}
		<a
			href={null}
			onclick={() => setDiskLetter?.(disk.letter)}
			class:border-primary={disk.letter === diskLetter}
			class="group relative flex h-fit w-96 flex-row items-center justify-between gap-3 rounded-lg border-2 p-3 hover:border-primary"
		>
			<span class="rounded-btn bg-white px-3 py-1 text-xl font-bold opacity-80">{disk.letter}:</span
			>
			{#if operation}
				<div class="flex flex-1 flex-col gap-3">
					<div class="flex-1">
						{operation.action}: {operation.surname} ({operation.carNumber}) [{operation.dateStr}]
					</div>
					<div class="relative h-8 flex-1 rounded-btn bg-neutral-200">
						<div
							style:width={`${(disk.usedGB / disk.totalSpaceGB) * 100}%`}
							class="striped h-full overflow-hidden rounded-btn"
						></div>
						<span
							class="absolute left-1/2 top-1/2 flex h-6 -translate-x-1/2 -translate-y-1/2 rounded-lg bg-white px-2 py-1 text-xs opacity-85 backdrop-blur-sm"
							>{(operation.progress * 100).toFixed(2)} %</span
						>
					</div>
					<div class="h-0 w-full overflow-hidden transition-all group-hover:h-8">
						<button class="btn btn-error btn-sm w-full">
							<IconCircleXFilled />
							Отменить</button
						>
					</div>
				</div>
			{:else}
				<div class="relative h-8 flex-1 rounded-btn bg-neutral-200">
					<div
						style:width={`${(disk.usedGB / disk.totalSpaceGB) * 100}%`}
						class="h-full rounded-btn bg-primary"
					></div>
					<span
						class="absolute left-1/2 top-1/2 flex h-6 -translate-x-1/2 -translate-y-1/2 rounded-lg bg-white px-2 py-1 text-xs opacity-85 backdrop-blur-sm"
						>{disk.usedGB} / {disk.totalSpaceGB} GB</span
					>
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
