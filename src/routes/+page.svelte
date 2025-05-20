<script lang="ts">
	import { writable } from 'svelte/store';
	export const refrainExperiments = writable<{ id: number; matched: number; percentage: number }[]>(
		[]
	);
	export const switchExperiments = writable<{ id: number; matched: number; percentage: number }[]>(
		[]
	);

	let experimentCount = 0;
	let iterationCount = 10_000;

	function handleExperiment(choice: string) {
		let matchCount = 0;
		if (choice === 'refrain') {
			for (let index = 0; index < iterationCount; index++) {
				let carDoor = Math.floor(Math.random() * 3) + 1;
				let userChoice = Math.floor(Math.random() * 3) + 1;

				if (carDoor == userChoice) matchCount++;
			}
			refrainExperiments.update((currAr) => {
				return [
					...currAr,
					{
						id: Date.now(),
						matched: matchCount,
						percentage: Math.floor((matchCount / iterationCount) * 100)
					}
				];
			});
		} else if (choice === 'switch') {
			for (let index = 0; index < iterationCount; index++) {
				let carDoor = Math.floor(Math.random() * 3) + 1;
				let userChoice = Math.floor(Math.random() * 3) + 1;

				let openedDoor;
				if (carDoor === 1 && userChoice === 1) openedDoor = 2;
				else if (carDoor === 1 && userChoice === 2) openedDoor = 3;
				else if (carDoor === 1 && userChoice === 3) openedDoor = 2;
				else if (carDoor === 2 && userChoice === 1) openedDoor = 3;
				else if (carDoor === 2 && userChoice === 2) openedDoor = 3;
				else if (carDoor === 2 && userChoice === 3) openedDoor = 1;
				else if (carDoor === 3 && userChoice === 1) openedDoor = 2;
				else if (carDoor === 3 && userChoice === 2) openedDoor = 1;
				else if (carDoor === 3 && userChoice === 3) openedDoor = 1;

				let secondUserChoice;
				if (userChoice === 1 && openedDoor === 2) secondUserChoice = 3;
				else if (userChoice === 1 && openedDoor === 3) secondUserChoice = 2;
				else if (userChoice === 2 && openedDoor === 1) secondUserChoice = 3;
				else if (userChoice === 2 && openedDoor === 3) secondUserChoice = 1;
				else if (userChoice === 3 && openedDoor === 1) secondUserChoice = 2;
				else if (userChoice === 3 && openedDoor === 2) secondUserChoice = 1;

				if (carDoor == secondUserChoice) matchCount++;
			}
			switchExperiments.update((currAr) => {
				return [
					...currAr,
					{
						id: Date.now(),
						matched: matchCount,
						percentage: Math.floor((matchCount / iterationCount) * 100)
					}
				];
			});
		}
		experimentCount++;
		matchCount = 0;
	}
</script>

<div class="min-h-screen bg-neutral-800 py-16 text-neutral-100">
	<div class="container mx-auto max-w-3xl">
		<h1 class="mb-4 text-center text-5xl font-bold text-neutral-50">Monty Hall Experiment</h1>
		<p class="mb-10 text-center text-neutral-400">
			A simulation to test the Monty Hall Problem: A probability puzzle based on a game show
			scenario. You pick one of three doors — one has a car, the others have goats. After your
			choice, the host (who knows what's behind each door) opens one of the other two doors,
			revealing a goat. You are then given a chance to switch. Should you?
		</p>
		<div class="grid grid-cols-3 gap-4">
			<button
				class="cursor-pointer rounded-xl border border-neutral-600 bg-neutral-700 px-3 py-1.5 text-lg text-neutral-100 transition-transform hover:bg-neutral-600 active:scale-95"
				on:click={() => handleExperiment('refrain')}
			>
				Run Refrain Simulation
			</button>
			<button
				class="cursor-pointer rounded-xl border border-neutral-600 bg-neutral-700 px-3 py-1.5 text-lg text-neutral-100 transition-transform hover:bg-neutral-600 active:scale-95"
				on:click={() => handleExperiment('switch')}
			>
				Run Switch Simulation
			</button>
			<button
				class="cursor-pointer rounded-xl border border-neutral-600 bg-neutral-700 px-3 py-1.5 text-lg text-neutral-100 transition-transform hover:bg-neutral-600 active:scale-95"
				on:click={() => {
					refrainExperiments.set([]);
					switchExperiments.set([]);
				}}
			>
				Reset Results
			</button>
		</div>
		<div class="my-6 mb-6 flex items-center gap-3 text-center">
			<label for="iterations" class="block text-sm">Iterations per Simulation</label>
			<input
				id="iterations"
				class="w-32 rounded px-2 text-center text-neutral-300"
				type="number"
				bind:value={iterationCount}
				min={1}
			/>
		</div>
		<div
			class="mt-6 grid grid-cols-2 gap-8 rounded-xl border border-neutral-700 px-3 py-2 text-lg text-neutral-200"
		>
			<div>
				<h3 class="mb-5 border-b border-b-neutral-700 pb-2 font-medium text-neutral-100">
					Refrain Strategy - <span class="text-sm">{$refrainExperiments.length} experiments</span>
				</h3>
				{#each $refrainExperiments as experiment}
					<div class="border-b border-b-neutral-600 py-1 text-sm">
						<h4>
							Matched: <span class="font-medium">
								{experiment.matched}
							</span>
						</h4>
						<h4 class="mt-0.5">
							Success Rate: <span class="font-medium text-neutral-50 underline"
								>{experiment.percentage}%</span
							>
						</h4>
					</div>
				{/each}
			</div>
			<div>
				<h3 class="mb-5 border-b border-b-neutral-700 pb-2 font-medium text-neutral-100">
					Switch Strategy - <span class="text-sm">{$switchExperiments.length} experiments</span>
				</h3>
				{#each $switchExperiments as experiment}
					<div class="border-b border-b-neutral-600 py-1 text-sm">
						<h4>
							Matched: <span class="font-medium">
								{experiment.matched}
							</span>
						</h4>
						<h4 class="mt-0.5">
							Success Rate: <span class="font-medium text-neutral-50 underline"
								>{experiment.percentage}%</span
							>
						</h4>
					</div>
				{/each}
			</div>
		</div>
	</div>
</div>
