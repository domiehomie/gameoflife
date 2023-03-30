<script lang="ts">
	let generation = 0;
	let running = false;

	let cells: number[][] = [
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 1, 1, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
		[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
	];

	const nextGen = (): void => {
		generation++;

		let toToggle: { x: number; y: number }[] = [];

		for (let [x, row] of cells.entries()) {
			for (let [y, cell] of row.entries()) {
				let neighbors = findNeighbors(x, y);
				let sum: number = neighbors.reduce((a, b) => a + b, 0);

				if (cell == 1) {
					if (sum <= 1) {
						toToggle.push({ x, y });
					} else if (sum >= 4) {
						toToggle.push({ x, y });
					}
				} else {
					if (sum == 3) {
						toToggle.push({ x, y });
					}
				}
			}
		}

		toToggle.forEach(({ x, y }) => {
			toggle(x, y);
		});
	};

	const MAX_X = 29;
	const MAX_Y = 11;

	const findNeighbors = (x: number, y: number): number[] => {
		let res: number[] = [];

		let search: { x: number; y: number }[] = [
			{ x: x - 1, y },
			{ x: x - 1, y: y - 1 },
			{ x: x - 1, y: y + 1 },
			{ x, y: y - 1 },
			{ x, y: y + 1 },
			{ x: x + 1, y: y - 1 },
			{ x: x + 1, y },
			{ x: x + 1, y: y + 1 }
		];

		search.forEach(({ x, y }) => {
			if (x < 0 || y < 0 || x > MAX_X || y > MAX_Y) return;
			res.push(cells[x][y]);
		});
		return res;
	};

	const toggle = (x: number, y: number) => {
		let before = cells[x][y];
		if (before == 1) {
			cells[x][y] = 0;
		} else {
			cells[x][y] = 1;
		}
	};

	const reset = () => {
		for (let [x, row] of cells.entries()) {
			for (let [y, cell] of row.entries()) {
				cells[x][y] = 0;
			}
		}
	};

	const start = (): void => {
		running = true;
		startTick();
	};

	const startTick = () => {
		if (!running) return;
		nextGen();
		setTimeout(startTick, 500);
	};

	const stop = (): void => {
		running = false;
	};
</script>

<div>
	<h1 class="text-2xl font-semibold">Generation {generation}</h1>
	<div class="flex">
		<div class="flex justify-self-center w-full divide-x-2 divide-gray-200">
			{#each cells as row, x}
				<div class="divide-y-2 divide-gray-200">
					{#each row as cell, y}
						{#if cell == 0}
							{#key cells}
								<div
									class="w-8 h-8 flex select-none cursor-pointer items-center justify-itemd-center drop-shadow-sm"
									on:click={() => toggle(x, y)}
									on:keypress={() => toggle(x, y)}
								>
									{findNeighbors(x, y).reduce((a, b) => a + b, 0)}
								</div>
							{/key}
						{:else}
							{#key cells}
								<div
									class="w-8 h-8 flex select-none text-center cursor-pointer items-center justify-itemd-center bg-violet-400   drop-shadow-sm"
									on:click={() => toggle(x, y)}
									on:keypress={() => toggle(x, y)}
								>
									{findNeighbors(x, y).reduce((a, b) => a + b, 0)}
								</div>
							{/key}
						{/if}
					{/each}
				</div>
			{/each}
		</div>
	</div>
	<div class="flex mx-4">
		{#if !running}
			<div
				class="h-fit mx-1 w-fit px-5 py-2 text-sm text-white font-semibold drop-shadow-md hover:bg-green-700 cursor-pointer select-none bg-green-500 rounded-md"
				on:keypress={start}
				on:click={start}
			>
				Start
			</div>
		{:else}
			<div
				class="h-fit mx-1 w-fit px-5 py-2 text-sm text-white font-semibold drop-shadow-md hover:bg-red-700 cursor-pointer select-none bg-red-500 rounded-md"
				on:keypress={stop}
				on:click={stop}
			>
				Stop
			</div>
		{/if}
		<div
			class="h-fit mx-1 w-fit px-5 py-2 text-sm text-white font-semibold drop-shadow-md hover:bg-violet-700 cursor-pointer select-none bg-violet-500 rounded-md"
			on:keypress={nextGen}
			on:click={nextGen}
		>
			Advance
		</div>
		<div
			class="h-fit mx-1 w-fit px-5 py-2 text-sm text-white font-semibold drop-shadow-md hover:bg-red-700 cursor-pointer select-none bg-red-500 rounded-md"
			on:keypress={reset}
			on:click={reset}
		>
			Reset
		</div>
	</div>
</div>
