<script generics="T" lang="ts">
	import { IconCircleCheckFilled } from '@tabler/icons-svelte';
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
	{#if focused}
		<ul
			transition:slide={{ duration: 250, axis: 'y' }}
			class="rounded-box absolute left-0 top-[calc(100%+0.5rem)] z-10 flex max-h-96 w-full flex-col gap-1 overflow-auto border bg-white bg-opacity-50 p-2 backdrop-blur-sm"
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
