<script lang="ts">
	import { createSolutionBuilderWithWatch } from 'typescript';
	import choices from '../data/choices.json';
	import scenarios from '../data/scenarios.json';

	type ChoiceData = {
		id: number;
		text: string;
		count: number;
	};

	type ScenarioChoice = {
		id: string;
		outcome: string;
	};

	type ChoiceMap = Record<string, ChoiceData>;
	type ScenarioMap = Record<string, ScenarioChoice[]>;

	const choiceMap = choices as ChoiceMap;
	const scenarioMap = scenarios as ScenarioMap;

	const scenarioTexts: Record<string, string> = {
		'scenario-1': 'Hello',
		'scenario-2': 'The turtle is laying eggs. How do you handle the situation?',
		'scenario-3': 'You have collected the eggs. Now what?',
		'scenario-4': 'You need to transport the eggs to the hatchery. How?',
		'scenario-5': 'At the hatchery, the eggs are ready to be incubated. What next?',
		// Add more scenario descriptions as needed
		'end-game-bad': 'Game over. The turtle population has suffered due to your choices.',
		'end-game-good': 'Congratulations! You have successfully conserved the turtle population.',
	};

	const scenarioImages: Record<string, string> = {
		'scenario-1': 'Turtle scenario 1.jpg',
		'scenario-2': 'Turtle scenario 2.jpg',
		'scenario-3': 'Turtle scenario 3.jpg',
		'scenario-4': 'scenario-4.jpg',
		'scenario-5': 'scenario-5.jpg',
		'end-game-bad': 'end-game-bad.jpg',
		'end-game-good': 'end-game-good.jpg',
	};

	let scenarioId = $state('scenario-1');
	let turtleCount = $state(100);
	let gameIsOver = $state(false);

	function endGame(count: number, scenario: string): boolean {
		if (count <= 0) {
			scenarioId = 'end-game-bad';
			return true;
		}
		return scenario === 'end-game-good';
	}
</script>

<div class="game-container">
	<h1>Can You Protect a Clutch of Eggs?</h1>


	<p>Turtle count: {turtleCount}</p>

	{#if scenarioImages[scenarioId]}
		<img class="scenario-image" src="{scenarioImages[scenarioId]}" alt="{scenarioTexts[scenarioId] || ''}" />
	{/if}

	<p>{scenarioTexts[scenarioId] || 'Scenario description not found.'}</p>

	<div class="card-container">
		{#if gameIsOver}
			<div class="game-over">
				<h1>GAME OVER</h1>
				<p>{choiceMap[scenarioMap[scenarioId]?.[0]?.id ?? 'choice-30']?.text}</p>
			</div>
		{:else}
			{#each scenarioMap[scenarioId] ?? [] as choice (choice.id)}
				<button
					class="card"
					onclick={() => {
						scenarioId = choice.outcome;
						turtleCount += choiceMap[choice.id].count;
						gameIsOver = endGame(turtleCount, scenarioId);
					}}>
					<p>{choiceMap[choice.id].text}</p>
				</button>
			{/each}
		{/if}
	</div>
</div>


<style>
	.game-container {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
		min-height: 100vh;
		text-align: center;
	}
	.card-container {
		display: flex;
		gap: 10px;
		flex-wrap: wrap;
	}
	.card {
		padding: 20px;
		border: 1px solid rgb(193, 218, 218) !important;
		background-color: rgb(193, 218, 218) !important;
		color: white;
		font-weight: bold;
		cursor: pointer;
		transition: background-color 0.2s;
	}
	.card:hover {
		background-color: rgb(151, 170, 170) !important;
	}

	.scenario-image {
		max-width: 100%;
		width: 320px;
		height: auto;
		border-radius: 12px;
		margin: 0 0 18px;
		box-shadow: 0 10px 25px rgba(0, 0, 0, 0.12);
	}

	.game-over {
		text-align: center;
		padding: 40px;
	}
</style>
