<script>
	import Display from './Display.svelte'

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

	const isOperator = (op) => (
			op === Add ||
			op === Subtract ||
			op === Divide ||
			op === Multiply ||
			op === Negate
		);

	const operatorToString = (op) => {
		switch (op) {
			case Add: 
				return "+";
			case Negate:
			case Subtract: 
				return "-";
			case Multiply:
				return "×";
			case Divide:
				return "÷";
			default:
				return "";
		}
	}

	const performOperation = (operandA, op, operandB) => {
		switch(op) {
			case Add:
				return operandA + operandB;
			case Subtract:
				return operandA - operandB;
			case Multiply:
				return operandA * operandB;			
			case Divide:
				return operandA / operandB;			
			default:
				return 0
		}
	}

	const equationTail = () => equation[equation.length - 1];
	
	const equationTailUpdate = (part) => {
		equation[equation.length - 1] = part;
	}

	const equationAppend = (part) => {
		equation = [...equation, part];
	}

	const onDecimal = () => {

		const tail = equationTail()
		if (typeof tail === "string") {
				
			const hasDecimal = tail.indexOf('.') !== -1
	
			if(hasDecimal) {
				return
			}
			
			equationTailUpdate(`${tail}.`)
		} else {
			equationAppend(`0.`)
		}
	}

	const onDigit = (digit) => {
		const tail = equationTail();
		if (typeof tail === "string") {
			equationTailUpdate(`${tail}${digit}`);
		} else {
			equationAppend(`${digit}`);
		}
	}

	const onOperator = (op) => {
		const tail = equationTail();

		if (isOperator(tail)) {
			if (op === Subtract) {
				if (tail !== Negate) {
					// Append negation
					equationAppend(Negate);
				}
			} else {
				// Replace current operator
				equationTailUpdate(op);
			}
		} else {
			if (op === Subtract && equation.length === 0) {
				equationAppend(Negate);
			} else {
				equationAppend(op);
			}
		}
	}

	const onClear = () => {
		equation = [];
	}

	$: display = `${equation
		.map((item) =>
			typeof item === "string" ? item : operatorToString(item)
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
			if (typeof item === "string") {
				const value = Number.parseFloat(item)
				if (operator) {
					result = performOperation(result, operator, sign * value);
				} else {
					result = sign * value;
				}
				sign = 1;
			}
		}
	}

</script>

<Display display={display} result={result} />

<div class="buttons">
	<button class="additon" on:click={() => onOperator(Add)}>+</button>
	<button class="subtract" on:click={() => onOperator(Subtract)}>-</button>
	<button class="multiply" on:click={() => onOperator(Multiply)}>×</button>
	<button class="divide" on:click={() => onOperator(Divide)}>÷</button>
	{#each digits as digit}
		<button class="white" on:click={() => onDigit(digit)}>{digit}</button>
	{/each}
	<button class="white" on:click={() => onDecimal()}>.</button>
	<button class="white" on:click={onClear}>C</button>
</div>

<style>
	.buttons {
		display: grid;
		grid-gap: .5rem;
		max-width: 100%;
	}
	
	button {
		color: #333;
		outline: none;
		padding: 0.8rem 0.4em;
		
		background: #f3f3f3;
		box-sizing: border-box;
		border: 1px solid #ccc;
		border-radius: 0.5rem;
		
		font-family: inherit;
		font-size: inherit;

		width: 100%;
		height: 100%;
	}
		
	.white {
		background: #fff;
	}

	.divide {
		grid-column: 4 / span 1;
		grid-row: 1;
	}
	.multiply {
		grid-column: 4 / span 1;
		grid-row: 2;
	}
	.subtract {
		grid-column: 4 / span 1;
		grid-row: 3;
	}
	.additon {
		grid-column: 4 / span 1;
		grid-row: 4;
	}
</style>
