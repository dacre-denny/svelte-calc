<script>
	export let display = '';
	export let result = '';
</script>

<div>
	<small>{display}</small>
	<h2>{result}</h2>
</div>

<style>
div > * {
	text-overflow: ellipsis;
	text-align: right;

	overflow: hidden;
	white-space: nowrap;
	width:100%;
}

small {
	display:block;
	
	min-height:1rem;
	line-height: 1;
	font-size:1rem;
	opacity:0.5;
}

h2 {
	font-size:2rem;
	font-weight: 100;
	
	margin:0;
}
</style>