<script>
	import { onMount } from 'svelte';

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

	let coinBalance = $state(10);
	let wishes = $state(['My First Wish', 'Get my framework 12']);
	let currentWish = $state('');

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

<h1>The Aether Well</h1>
<h2>Coins: {coinBalance}</h2>
<div>
	<h2>Wishes</h2>

	<ol>
		{#each wishes as wish}
			<li>{wish}</li>
		{/each}
	</ol>
</div>

<input
	bind:value={currentWish}
	type="text"
	placeholder="Enter a wish..."
	onkeydown={(e) => {
		if (e.key === 'Enter') {
			makeWish();
		}
	}}
/>

<button onclick={makeWish} disabled={coinBalance === 0}>Make Wish</button>
