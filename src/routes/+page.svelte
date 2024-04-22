<script lang="ts">
	import { onMount } from 'svelte';

	let decimal = '';
	let octal = '';
	let binary = '';
	let hexadecimal = '';
	let bits: string[] = Array(64).fill('0');

	function updateAllFromBits() {
		const num = BigInt('0b' + bits.slice().reverse().join(''));
		updateAllFromNumber(num);
	}

	function updateAllFromNumber(num: bigint) {
		decimal = num.toString(10);
		octal = num.toString(8);
		binary = num.toString(2).padStart(64, '0');
		hexadecimal = num.toString(16).toUpperCase().padStart(16, '0');
		updateBitsFromNumber(num);
	}

	function updateBitsFromNumber(num: bigint) {
		const bitString = num.toString(2).padStart(64, '0');
		bits = bitString.split('').reverse();
	}

	// Event handlers for input conversions
	function convertFromDecimal(value: string) {
		updateFromInput(value, 10);
	}
	function convertFromOctal(value: string) {
		updateFromInput(value, 8);
	}
	function convertFromBinary(value: string) {
		updateFromInput(value, 2);
	}
	function convertFromHexadecimal(value: string) {
		updateFromInput(value, 16);
	}

	function updateFromInput(value: string, base: number) {
		let num = BigInt(0);
		try {
			num = BigInt(`0x${BigInt(value).toString(16)}`);
			updateAllFromNumber(num);
		} catch (e) {
			clearAll();
		}
	}

	function clearAll() {
		decimal = octal = binary = hexadecimal = '';
		bits.fill('0');
	}

	function toggleBit(index: number) {
		bits[index] = bits[index] === '0' ? '1' : '0';
		updateAllFromBits();
	}

	function handleKeyDown(index: number) {
		return function (event: KeyboardEvent) {
			if (event.key === 'Enter' || event.key === ' ') {
				toggleBit(index);
				event.preventDefault();
			}
		};
	}
</script>

<div style="margin-left: 1em;">
	<h1>Calculator</h1>
	<div>
		<label for="decimal">Decimal:</label>
		<input
			id="decimal"
			type="text"
			bind:value={decimal}
			on:input={() => convertFromDecimal(decimal)}
			placeholder="Decimal"
		/>

		<label for="octal">Octal:</label>
		<input
			id="octal"
			type="text"
			bind:value={octal}
			on:input={() => convertFromOctal(octal)}
			placeholder="Octal"
		/>

		<label for="binary">Binary:</label>
		<input
			id="binary"
			type="text"
			bind:value={binary}
			on:input={() => convertFromBinary(binary)}
			placeholder="Binary"
		/>

		<label for="hexadecimal">Hexadecimal:</label>
		<input
			id="hexadecimal"
			type="text"
			bind:value={hexadecimal}
			on:input={() => convertFromHexadecimal(hexadecimal)}
			placeholder="Hexadecimal"
		/>
	</div>

	<div>
		<div>Bit Values:</div>
		<div>
			<div class="bit-container">
				{#each Array(8) as _, i}
					<div class="bit-group">
						{#each bits.slice(i * 8, i * 8 + 8) as bit, index}
							<div>
								<div>
									<button
										type="button"
										class="bit-box {bit === '1' ? 'on' : ''}"
										on:click={() => toggleBit(i * 8 + index)}
										on:keydown={handleKeyDown(i * 8 + index)}
										aria-label={`Toggle bit ${i * 8 + index}`}
										aria-pressed={bit === '1'}
									>
										{bit}
									</button>
								</div>
								<div class="bit-index">{i * 8 + index}</div>
							</div>
						{/each}
					</div>
				{/each}
			</div>
		</div>
	</div>
</div>

<style>
	input,
	label,
	div,
	button,
	h2,
	h1 {
		font-family: 'JetBrains Mono', monospace;
	}

	input[type='text'] {
		width: 95%; /* Adjust width to fill container */
		margin: 10px 2.5%; /* Center and provide vertical space */
		padding: 8px;
		font-size: 16px; /* Increase font size for readability */
	}

	button.bit-box {
		display: inline-block;
		width: 40px;
		height: 40px;
		margin: 2px;
		padding: 0;
		border: none;
		background-color: #eee;
		text-align: center;
		line-height: 20px;
		cursor: pointer;
		color: black;
		outline: none;
		font-size: large;
	}
	button.bit-box.on {
		background-color: #666;
		color: white;
	}

	.bit-container {
		display: flex;
		flex-wrap: wrap;
		justify-content: start;
		align-items: center;
	}
	.bit-group {
		display: flex;
		min-width: 10em;
		margin-left: 3em;
		margin-bottom: 1em;
	}
	.bit-index {
		font-size: small;
		color: #a0a0a0;
	}

	/* .bit-container div:nth-child(8n) {
		margin-right: 1em;
	} */
</style>
