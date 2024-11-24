<script generics="T" lang="ts">
	import { IconCircleCheckFilled, IconX } from '@tabler/icons-svelte';
	import type { HTMLAttributes } from 'svelte/elements';
	import { slide } from 'svelte/transition';

	type Props = {
		options?: T[];
		value?: T;
		setValue?: (value: T) => any;
	} & HTMLAttributes<HTMLInputElement>;

	const { options, value, setValue, ...attrs }: Props = $props();

	let focused = $state(false);
</script>

<div class="relative w-fit">
	<input
		{...attrs}
		onfocus={() => (focused = true)}
		onblur={() => (focused = false)}
		oninput={(e) => {
			setValue?.(e.currentTarget.value as T);
		}}
		{value}
		class="input input-sm input-bordered w-56"
	/>
	{#if value}
		<button
			onclick={() => {
				// @ts-ignore
				setValue('');
			}}
			class="absolute right-2 top-1/2 -translate-y-1/2 rounded-full bg-base-content/20 p-0.5 text-base-100"
		>
			<IconX size={12} />
		</button>
	{/if}
	{#if focused}
		<ul
			transition:slide={{ duration: 250, axis: 'y' }}
			class="absolute left-0 top-[calc(100%+0.5rem)] z-10 flex max-h-96 w-full flex-col gap-1 overflow-auto rounded-box border border-base-content/50 bg-base-100/50 p-2 backdrop-blur-sm"
		>
			{#each options ?? [] as option}
				<button
					onpointerdown={(e) => {
						setValue?.(option);
					}}
					class="btn btn-sm justify-between"
				>
					<span>{option}</span>
					{#if option === value}
						<IconCircleCheckFilled class="text-success" size={16} />
					{/if}
				</button>
			{/each}
		</ul>
	{/if}
</div>
