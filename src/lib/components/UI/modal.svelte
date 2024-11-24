<script lang="ts">
	import type { Action } from 'svelte/action';
	import type { Snippet } from 'svelte';
	import type { HTMLAttributes, PointerEventHandler } from 'svelte/elements';
	import { fade, scale } from 'svelte/transition';

	type Props = {
		onClose?: () => any;
		children?: Snippet;
	} & HTMLAttributes<HTMLDivElement>;

	const { children, onClose, ...attrs }: Props = $props();

	const portal: Action = (node) => {
		$effect(() => {
			document.body.append(node);
			const listener: PointerEventHandler<HTMLElement> = (e) => {
				// @ts-ignore
				if (e.target === modalBoxElement || modalBoxElement?.contains(e.target)) return;
				onClose?.();
			};
			// @ts-ignore
			document.documentElement.addEventListener('pointerdown', listener);
			return () => {
				// @ts-ignore
				document.documentElement.removeEventListener('pointerdown', listener);
				node.remove();
			};
		});
	};

	let modalBoxElement = $state<HTMLDivElement>();
</script>

<div
	transition:fade={{ duration: 230 }}
	class="absolute left-0 top-0 flex h-full w-full items-center justify-center bg-base-300/50 backdrop-blur-sm"
	use:portal
>
	<div transition:scale={{ duration: 230 }} bind:this={modalBoxElement} {...attrs}>
		{@render children?.()}
	</div>
</div>
