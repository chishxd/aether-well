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

		// Logic for Daily Login thing.
		const currentDate = new Date().toDateString();
		const lastVisit = localStorage.getItem('aether-well-last-visit');

		if (lastVisit && lastVisit !== currentDate) {
			coinBalance += 5;
		}
		localStorage.setItem('aether-well-last-visit', currentDate);
	});

	async function makeWish() {
		if (currentWish.trim() === '' || coinBalance === 0) {
			return;
		}

		coinBalance -= 1;
		wishes.unshift(currentWish);
		currentWish = '';

		playPop = true;
		setTimeout(() => {
			playPop = false;
		}, 300);
	}

	let playPop = $state(false);

	$effect(() => {
		localStorage.setItem('aether-well-coins', JSON.stringify(coinBalance));
	});

	$effect(() => {
		localStorage.setItem('aether-well-wishes', JSON.stringify(wishes));
	});
</script>

<div
	class="flex h-screen w-screen flex-col justify-between bg-cover bg-center p-4 md:p-8"
	style="background-image: url('/background.jpg');"
>
	<header class="mb-10 text-center">
		<h1 class="pt-8 text-center font-cinzel text-5xl text-white">The Aether Well</h1>

		<!-- Personal Notes here! -->
		<!-- mx: margin X, mt: margin-top-->
		<h2
			class="mx-auto
			mt-2 w-fit rounded-lg
			 bg-slate-900/30 p-4 text-xl
			 text-slate-200 ring-1 ring-amber-400
			  backdrop-blur-md"
		>
			✨ Coins:
			<span class="inline-block font-bold text-amber-500" class:coin-pop={playPop}>
				{coinBalance}
			</span>
		</h2>
	</header>

	<div class="mb-10 text-center">
		<!-- tracking-widest sets font-spacing-->
		<h2 class="mb-6 text-3xl font-bold tracking-widest text-slate-200 uppercase">Wishes</h2>
		<!-- Only 30% of screen is wishes :sob: I think it's a sweet spot though -->
		<ol class="mx-auto max-h-[30vh] max-w-2xl overflow-y-auto">
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

	<div class="flex gap-2">
		<!-- SOOO many classes~~~ I'll list how some of them work just for myself-->
		<!-- w-full: sets width to 100% of it's container's size... i.e width:100%
		  appeareance-none: remove default CSS by browser-->
		<input
			class="w-full appearance-none rounded-lg
			border
			border-slate-500/50 bg-slate-900/30 p-3 text-white
			backdrop-blur-md
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
