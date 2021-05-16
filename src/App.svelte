<script>
	import Nested from "./Nested.svelte";
	
	let result = "";
	let equation = [];
	const digits = [
		7,8,9,
		4,5,6,
		1,2,3,
		0
];

	const Add = Symbol("Add");
	const Subtract = Symbol("Subtract");
	const Multiply = Symbol("Multiply");
	const Divide = Symbol("Divide");
	const Negate = Symbol("Negate");

	function operatorToString(op) {
		switch (op) {
			case Add: {
				return "+";
			}
			case Negate:
			case Subtract: {
				return "-";
			}
			case Multiply: {
				return "×";
			}
			case Divide: {
				return "÷";
			}
			default: {
				return "";
			}
		}
	}

	function performOperation(operandA, op, operandB) {
		if (op === Add) {
			return operandA + operandB;
		}
		if (op === Subtract) {
			return operandA - operandB;
		}
		if (op === Multiply) {
			return operandA * operandB;
		}
		if (op === Divide) {
			return operandA / operandB;
		}
		return 0;
	}

	$: display = `${equation
		.map((item) =>
			typeof item === "number" ? item : operatorToString(item)
		)
		.join("")}`;
	$: {
		
		let operator = "";
		let sign = 1;
		result = 0;

		for (let item of equation) {
			if (isOperator(item)) {
				if (item === Negate) {
					sign = -1;
				} else {
					operator = item;
				}
			}
			if (typeof item === "number") {
				if (operator) {
					result = performOperation(result, operator, sign * item);
				} else {
					result = sign * item;
				}
				console.log("sign", sign);

				sign = 1;
			}
		}
	}

	function handleDigit(digit) {
		const tail = equation[equation.length - 1];
		if (typeof tail === "number") {
			equation[equation.length - 1] = Number.parseFloat(
				`${tail}${digit}`
			);
		} else {
			equation = [...equation, digit];
		}
	}

	function isOperator(op) {
		return (
			op === Add ||
			op === Subtract ||
			op === Divide ||
			op === Multiply ||
			op === Negate
		);
	}

	function handleOperator(op) {
		const tail = equation[equation.length - 1];

		if (isOperator(tail)) {
			if (op === Subtract) {
				if (tail !== Negate) {
					// Append negation
					equation = [...equation, Negate];
				}
			} else {
				// Replace current operator
				equation[equation.length - 1] = op;
			}
		} else {
			if (op === Subtract && equation.length === 0) {
				equation = [...equation, Negate];
			} else {
				equation = [...equation, op];
			}
		}
	}

	function handleClear() {
		equation = [];
	}

</script>

<main>
	<h1>Sevlte Calc</h1>

	<div class="display">
		<div class="elipse equation">{display}</div>
		<div class="elipse result">{result}</div>
	</div>

	<div class="calculator">
		<button class="add" on:click={() => handleOperator(Add)}>+</button>
		<button class="sub" on:click={() => handleOperator(Subtract)}>-</button>
		<button class="mul" on:click={() => handleOperator(Multiply)}>×</button>
		<button class="div" on:click={() => handleOperator(Divide)}>÷</button>
		{#each digits as digit}
			<button class="digit" on:click={() => handleDigit(digit)}>{digit}</button>
		{/each}
		<button class="digit" on:click={() => handleDigit('.')}>.</button>
		<button class="digit" on:click={handleClear}>C</button>
	</div>
</main>

<style>
	main {
		max-width: 280px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-align: center;
		text-transform: uppercase;
		font-size: 3em;
		font-weight: 100;
	}
	
	button {
		background: white;
		border-radius: 0.5rem;
		width: 100%;
		height: 100%;
	}

	.calculator {
		display: grid;
		grid-gap: .5rem;
		max-width: 100%;
	}

	.display {
		
		font-size: 2em;
		font-weight: 100;
		
		text-align: right;
		
		margin-bottom: 1rem;

		grid-column: 1 / span 4;
		grid-row: 1;

		padding-top:1.5rem;
		position: relative;
	}

	.display > div {
		width:100%;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.equation {
		position: absolute;
		top:0;
		right:0;
		font-size:1rem;
		padding-right: .25rem;
	}
	.result {
		font-size:2rem;
		padding-right: .25rem;
	}

	.div {
		grid-column: 4 / span 1;
		grid-row: 1;
		background: #f3f3f3;
	}
	.mul {
		grid-column: 4 / span 1;
		grid-row: 2;
		background: #f3f3f3;
	}
	.sub {
		grid-column: 4 / span 1;
		grid-row: 3;
		background: #f3f3f3;
	}
	.add {
		grid-column: 4 / span 1;
		grid-row: 4;
		background: #f3f3f3;
	}
</style>
