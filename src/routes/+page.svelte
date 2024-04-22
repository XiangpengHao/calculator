<script lang="ts">
	import { onMount } from 'svelte';
	import { writable } from 'svelte/store';

	let message = writable('');

	let decimal = '';
	let octal = '';
	let octal_pretty = '';
	let binary = '';
	let binary_pretty = '';
	let hexadecimal = '';
	let hexadecimal_pretty = '';
	let bits: string[] = Array(64).fill('0');

	function updateAllFromBits() {
		const num = BigInt('0b' + bits.slice().reverse().join(''));
		updateAllFromNumber(num);
	}

	function updateAllFromNumber(num: bigint) {
		decimal = num.toString(10);
		octal = num.toString(8);
		octal_pretty = '0o' + octal.match(/.{1,3}/g)!.join('_');

		binary = num.toString(2).padStart(64, '0');
		binary_pretty = '0b' + binary.match(/.{1,8}/g)!.join('_');
		hexadecimal = num.toString(16).toUpperCase().padStart(16, '0');
		hexadecimal_pretty = '0x' + hexadecimal.match(/.{1,4}/g)!.join('_');
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

	async function copyToClipboard(text: string) {
		try {
			await navigator.clipboard.writeText(text);
			message.set('Copied to Clipboard!');
			setTimeout(() => message.set(''), 2000); // Clear message after 2 seconds
		} catch (err) {
			message.set('Failed to copy!');
			setTimeout(() => message.set(''), 2000);
		}
	}

	function handleKeyPress(event: any, text: string) {
		if (event.key === 'Enter' || event.key === ' ') {
			copyToClipboard(text);
			event.preventDefault(); // Prevent the default action for these keys
		}
	}

	function updateFromInput(value: string, base: number) {
		let num = BigInt(0);
		if (base === 2) {
			num = BigInt(`0b${value}`);
		} else if (base === 8) {
			num = BigInt(`0o${value}`);
		} else if (base === 16) {
			num = BigInt(`0x${value}`);
		} else if (base === 10) {
			num = BigInt(value);
		}
		try {
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
	<h1>Bit Calculator</h1>
	<div class="input-box">
		<label for="decimal">Decimal:</label>
		<div>
			<input
				id="decimal"
				type="text"
				bind:value={decimal}
				on:input={() => convertFromDecimal(decimal)}
				placeholder="Decimal"
			/>
		</div>
	</div>

	<div class="input-box">
		<label for="octal">Octal:</label>
		<div>
			<input
				id="octal"
				type="text"
				bind:value={octal}
				on:input={() => convertFromOctal(octal)}
				placeholder="Octal"
			/>
		</div>
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<!-- svelte-ignore a11y-no-static-element-interactions -->
		<div class="alternative-format" on:click={() => copyToClipboard(octal_pretty)}>
			{octal_pretty}
		</div>
	</div>

	<div class="input-box">
		<label for="binary">Bin:</label>
		<div>
			<input
				id="binary"
				type="text"
				bind:value={binary}
				on:input={() => convertFromBinary(binary)}
				placeholder="Bin"
			/>
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<!-- svelte-ignore a11y-no-static-element-interactions -->
			<div class="alternative-format" on:click={() => copyToClipboard(binary_pretty)}>
				{binary_pretty}
			</div>
		</div>
	</div>
	<div class="input-box">
		<label for="hexadecimal">Hex:</label>
		<div class="values">
			<input
				id="hexadecimal"
				type="text"
				bind:value={hexadecimal}
				on:input={() => convertFromHexadecimal(hexadecimal)}
				placeholder="Hex"
			/>
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<!-- svelte-ignore a11y-no-static-element-interactions -->
			<div class="alternative-format" on:click={() => copyToClipboard(hexadecimal_pretty)}>
				{hexadecimal_pretty}
			</div>
		</div>
	</div>

	<div class="input-box">
		<div>Bit bits:</div>
		<div>
			<div class="bit-container">
				{#each Array.from({ length: 8 }, (_, i) => 7 - i) as i}
					<div class="bit-group">
						{#each [...bits.slice(i * 8, i * 8 + 8)].reverse() as bit, index}
							<div class="one-bit">
								<div>
									<button
										type="button"
										class="bit-box {bit === '1' ? 'on' : ''}"
										on:click={() => toggleBit(i * 8 + 7 - index)}
										on:keydown={handleKeyDown(i * 8 + 7 - index)}
										aria-label={`Toggle bit ${i * 8 + 7 - index}`}
										aria-pressed={bit === '1'}
									>
										{bit}
									</button>
								</div>
								<div class="bit-index">{i * 8 + 8 - index}</div>
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
	h1 {
		font-family: 'JetBrains Mono', monospace;
	}

	input[type='text'] {
		width: 95%;
		margin: 10px 2.5%; /* Center and provide vertical space */
		margin-bottom: 0;
		margin-top: 0;
		padding: 8px;
		font-size: 16px; /* Increase font size for readability */
	}
	.alternative-format {
		margin: 10px 2.5%;
		margin-bottom: 0;
		margin-top: 0;
		padding: 8px;
		color: rgb(119, 135, 135);
	}

	.alternative-format:hover {
		cursor: pointer;
	}

	button.bit-box {
		display: inline-block;
		width: 35px;
		height: 35px;
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

	.input-box {
		margin-bottom: 1em;
	}

	/* .values {
		margin-bottom: 1em;
	} */

	.bit-container {
		display: flex;
		flex-wrap: wrap;
		justify-content: start;
		align-items: center;
	}
	.bit-group {
		display: flex;
		min-width: 10em;
		margin-left: 2em;
		margin-bottom: 1em;
	}

	.bit-group .one-bit:nth-child(5) {
		margin-left: 0.5em;
	}

	.bit-index {
		font-size: small;
		color: #a0a0a0;
	}
</style>
