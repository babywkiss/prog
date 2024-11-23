<script lang="ts">
	import Select from '$lib/components/UI/select.svelte';
	import { IconCalendarFilled, IconCarFilled, IconUserFilled } from '@tabler/icons-svelte';

	export type Info = {
		carNumber: string;
		surname: string;
		dateStr: string;
	};

	const carNumbers = $state<string[]>(['097', '098', '940']);
	const surnames = $state<string[]>(['Суслов', 'Гончаренко']);

	const now = new Date();
	const isMorning = now.getHours() < 12;
	const currentDateStr = `${now.getDate() - (isMorning ? 1 : 0)}${isMorning ? '-' : ''}${isMorning ? now.getDate() : ''}${now.toLocaleDateString('ru').slice(2)}`;
	const prevDateStr = `${now.getDate() - 1}${!isMorning ? '-' : ''}${!isMorning ? now.getDate() : ''}${now.toLocaleDateString('ru').slice(2)}`;

	const datesStrs = [currentDateStr, prevDateStr];

	type Props = {
		info: Info;
	};

	const { info }: Props = $props();

	$effect(() => {
		info.dateStr = datesStrs[0];
	});
</script>

<div class="flex flex-col gap-3">
	<div class="flex flex-col gap-1">
		<span class="ml-1 text-sm opacity-70">
			<IconCarFilled size={18} class="inline" />
			Номер автомобиля</span
		>
		<Select
			placeholder="XXX"
			setValue={(value) => (info.carNumber = value)}
			value={info.carNumber}
			options={carNumbers}
		/>
	</div>

	<div class="flex flex-col gap-1">
		<span class="ml-1 text-sm opacity-70">
			<IconUserFilled size={18} class="inline" />
			Фамилия</span
		>
		<Select
			placeholder="Иванов"
			setValue={(value) => (info.surname = value)}
			value={info.surname}
			options={surnames}
		/>
	</div>

	<div class="flex flex-col gap-1">
		<span class="ml-1 text-sm opacity-70">
			<IconCalendarFilled size={18} class="inline" />
			Дата</span
		>
		<Select
			placeholder="ДД.ММ.ГГГГ"
			setValue={(value) => (info.dateStr = value)}
			value={info.dateStr}
			options={datesStrs}
		/>
	</div>
</div>
