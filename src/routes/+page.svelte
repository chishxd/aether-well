<script>
	import { onMount } from 'svelte';
	import { fade, fly } from 'svelte/transition';
	import { flip } from 'svelte/animate';

	let coinBalance = $state(10);

	let currentWish = $state('');

	let showTooltip = $state(false);

	let wishes = $state([
		{ id: 1, text: 'My First Wish' },
		{ id: 2, text: 'Get my framework 12' }
	]);

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
		if (currentWish.trim() === '') {
			return;
		}

		if (coinBalance === 0) {
			showTooltip = true;

			setTimeout(() => {
				showTooltip = false;
			}, 2000);

			return;
		}

		const newWish = {
			id: Date.now(),
			text: currentWish
		};

		coinBalance -= 1;
		wishes.unshift(newWish);
		currentWish = '';

		playPop = true;
		setTimeout(() => {
			playPop = false;
		}, 300);
	}

	function handleKeyDown(event) {
		// If the user presses enter WITHOUT pressign shift
		if (event.key === 'Enter' && !event.shiftKey) {
			// Prevent the default operation on enter, i.e add a new line.
			event.preventDefault();

			makeWish();
		}
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

	<!-- Container to Store Wishes title and all wishes -->
	<div class="mx-auto mb-10 flex w-full max-w-2xl flex-1 flex-col overflow-hidden text-center">
		<!-- tracking-widest sets font-spacing-->
		<h2 class="mb-6 text-3xl font-bold tracking-widest text-slate-200 uppercase">Wishes</h2>
		<!-- Only 30% of screen is wishes :sob: I think it's a sweet spot though -->
		<ol class="flex-1 overflow-y-auto">
			{#each wishes as wish (wish.id)}
				<li
					in:fly={{ y: -20, duration: 400 }}
					animate:flip={{ duration: 300 }}
					class="mb-2 rounded-lg bg-slate-900/30 p-4
				 	break-words whitespace-pre-wrap
				 	text-slate-100 backdrop-blur-md"
				>
					{wish.text}
				</li>
			{/each}
		</ol>
	</div>

	<!-- This container stores the input area and Wish button -->
	<div class="relative flex gap-2">
		{#if showTooltip}
			<div
				in:fly={{ y: 10, duration: 200 }}
				out:fly={{ y: 10, duration: 200 }}
				class="absolute -top-14 left-1/2
		-translate-x-1/2 rounded-md
		bg-slate-900 px-3
		py-2 text-sm
		font-medium whitespace-nowrap text-white
		ring-1 ring-slate-600"
			>
				You are out of coins. Return Tomorrow to get some coins!
			</div>
		{/if}

		<!-- SOOO many classes~~~ I'll list how some of them work just for myself-->
		<!-- w-full: sets width to 100% of it's container's size... i.e width:100%
		  appeareance-none: remove default CSS by browser-->
		<textarea
			class="w-full resize-none appearance-none
			rounded-lg
			border border-slate-500/50 bg-slate-900/30 p-3
			text-white
			backdrop-blur-md
			placeholder:text-slate-400 focus:ring-2
			focus:ring-amber-400 focus:outline-none"
			rows="1"
			bind:value={currentWish}
			type="text"
			placeholder="Enter a wish..."
			onkeydown={handleKeyDown}
		></textarea>
		<button
			class="rounded-lg bg-amber-400 p-3 font-bold text-black
				transition-all duration-200
				hover:bg-amber-500
				disabled:cursor-not-allowed disabled:bg-slate-600"
			onclick={makeWish}
			onmouseenter={() => {
				if (coinBalance === 0) {
					showTooltip = true;
				}
			}}
			onmouseleave={() => {
				showTooltip = false;
			}}
			disabled={coinBalance === 0}>Make Wish</button
		>
	</div>
</div>
