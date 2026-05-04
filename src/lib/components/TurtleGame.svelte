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
		'scenario-1': 'A mother turtle comes ashore to lay her eggs. What do you do?',
		'scenario-2': 'The mother turtle starts laying her clutch. You need to get the eggs from her. What do you do?',
		'scenario-3': 'You have the eggs from her. Now you need to transport them. What do you do?',
		'scenario-4': 'The eggs are in the bucket. Now you need to get them to the hatchery. What do you do?',
		'scenario-5': 'Now you need to burry the eggs. What do you do?',
		'scenario-6': 'The hole is dug. Before you put the eggs in, do you...',
		'scenario-7': 'You are ready to bury the eggs. What do you do?',
		'scenario-8': 'The eggs are in the nest. Do you...',
		'scenario-9': 'You are ready to cover the eggs with sand. What do you do?',
		'scenario-10': 'Now the eggs will incubate. Should you...',
		'scenario-11': 'The eggs have hatched! Now you need to get them into the ocean. What do you do?',
		'scenario-12': 'It is currently nightime. What do you do?',
		'scenario-13': 'The hatchlings need to get to the ocean. What do you do?',
		'scenario-14': 'The hatchlings are almost there! What do you do?',
		// Add more scenario descriptions as needed
		'end-game-bad': 'You have killed all the baby turtles.',
		'end-game-good': 'Congratulations! A good portion of the hatchlings made it to the ocean!',
	};

	const scenarioImages: Record<string, string> = {
		'scenario-1': 'Turtle scenario 1.jpg',
		'scenario-2': 'Turtle scenario 2.jpg',
		'scenario-3': 'Turtle scenario 3.jpg',
		'scenario-4': 'Turtle scenario 4.jpg',
		'scenario-5': 'Turtle hole.png',
		'scenario-6': 'Turtle scenario 6.jpg',
		'scenario-7': 'Turtle scenario 7.jpg',
		'scenario-8': 'Turtle scenario 8.png',
		'scenario-9': 'Turtle scenario 9.png',
		'scenario-10': 'Turtle scenario 10.png',
		'scenario-11': 'Turtle scenario 11.png',
		'scenario-12': 'Turtle scenario 12.png',
		'scenario-13': 'Turtle scenario 13.jpg',
		'scenario-14': 'Turtle scenario 14.png',
		'end-game-bad': 'Turtles died.png',
		'end-game-good': 'Turtles alive.png',
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

	<p class="scenario-text">{scenarioTexts[scenarioId] || 'Scenario description not found.'}

	</p>

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
		/* flex-wrap: 1; */
		align-items:center;
		flex-direction: column;
	}
	@media (min-width: 700px) {
    .card-container {
        flex-direction: row;
    }
}
	.card {
		padding: 20px;
		border: 1px solid rgb(193, 218, 218) !important;
		background-color: rgb(193, 218, 218) !important;
		color: white;
		font-weight: bold;
		cursor: pointer;
		transition: background-color 0.2s;
		flex: 1 1 0;
		width: 100%;
	}
	.card:hover {
		background-color: rgb(151, 170, 170) !important;
	}

	.scenario-text{
		font-size: 1.3em;
		font-weight:700;
		max-width:80%;
	}

	.scenario-image {
		max-width: 100%;
		width: 320px;
		height: auto;
		border-radius: 12px;
		margin: 0 0 18px;
	}

	.game-over {
		text-align: center;
		padding: 40px;
	}
</style>
