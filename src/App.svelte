<script>
	import Header from './Header.svelte'
	import Calculator from './Calculator.svelte'
</script>

<main>
	<Header/>
	<Calculator />
</main>

<style>
	main {
		max-width:100%;
		width: 280px;
		margin: 0 auto;

		display:flex;
		flex-direction: column;
		gap:1rem;
	}
</style>
