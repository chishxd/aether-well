<script>
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';

	let coinBalance = $state(10);
	let wishes = $state(['My First Wish', 'Get my framework 12']);
	let currentWish = $state('');

	onMount(() => {
		const savedCoins = localStorage.getItem('aether-well-coins');
		if (savedCoins) {
			coinBalance = JSON.parse(savedCoins);
		}

		const savedWishes = localStorage.getItem('aether-well-wishes');
		if (savedWishes) {
			wishes = JSON.parse(savedWishes);
		}
	});

	async function makeWish() {
		if (currentWish.trim() === '' || coinBalance === 0) {
			return;
		}

		coinBalance -= 1;

		wishes.unshift(currentWish);

		currentWish = '';
	}

	$effect(() => {
		localStorage.setItem('aether-well-coins', JSON.stringify(coinBalance));
	});

	$effect(() => {
		localStorage.setItem('aether-well-wishes', JSON.stringify(wishes));
	});
</script>

<!-- Below this lies the HTML for my website -->

<div
	class="flex h-screen w-screen flex-col justify-between bg-cover bg-center p-4 md:p-8"
	style="background-image: url('/background.jpg');"
>
	<!-- This is the Heading -->
	<header class="mb-10 text-center">
		<h1 class="pt-8 text-center font-cinzel text-5xl text-white">The Aether Well</h1>
		<h2 class="text-xl text-slate-200">
			✨ Coins: <span class="font-bold text-amber-500">{coinBalance}</span>
		</h2>
	</header>

	<!-- These are all the Wishes!! -->
	<div class="mb-10 text-center">
		<h2 class="mb-6 text-3xl font-bold text-slate-200 uppercase tracking-widest">Wishes</h2>

		<ol class="mx-auto max-h-[5r0vh] max-w-2xl overflow-y-auto">
			{#each wishes as wish}
				<li
					in:fade
					class="mb-2 rounded-lg bg-slate-900/30
				 p-4 text-slate-100
				 backdrop-blur-md"
				>
					{wish}
				</li>
			{/each}
		</ol>
	</div>

	<!-- This container has input and 'Make Wish' Button -->
	<div class="flex gap-2">
		<input
			class="w-full appearance-none rounded-lg
			border border-slate-500/50
			bg-slate-900/30 p-3
			text-white backdrop-blur-md
			placeholder:text-slate-400
			focus:ring-2 focus:ring-amber-400
			focus:outline-none"
			bind:value={currentWish}
			type="text"
			placeholder="Enter a wish..."
			onkeydown={(e) => {
				if (e.key === 'Enter') {
					makeWish();
				}
			}}
		/>

		<button
			class="rounded-lg bg-amber-400 p-3 font-bold text-black
				transition-all duration-200
				hover:bg-amber-500
				disabled:cursor-not-allowed disabled:bg-slate-600"
			onclick={makeWish}
			disabled={coinBalance === 0}>Make Wish</button
		>
	</div>
</div>
