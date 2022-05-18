<script lang="ts">
	import { browser } from '$app/env';
	import Button from '$lib/components/Button.svelte';

	let error: string;
	let result: boolean;
	let riddle: { answers: string[]; imageUrl: string; imageAlt: string } = {
		answers: ['auto', 'samochód'],
		imageUrl: 'auto.png',
		imageAlt: 'auto'
	};

	if (browser) {
		/* @ts-ignore */
		const SpeechRecognition = window.SpeechRecognition || webkitSpeechRecognition;
		/* @ts-ignore */
		const SpeechGrammarList = window.SpeechGrammarList || webkitSpeechGrammarList;
		/* @ts-ignore */
		const SpeechRecognitionEvent = window.SpeechRecognitionEvent || webkitSpeechRecognitionEvent;

		const grammar =
			'#JSGF V1.0; grammar colors; public <color> = ' + riddle.answers.join(' | ') + ' ;';

		const recognition = new SpeechRecognition();
		const speechRecognitionList = new SpeechGrammarList();

		speechRecognitionList.addFromString(grammar, 1);

		recognition.grammars = speechRecognitionList;
		recognition.continuous = false;
		recognition.lang = 'pl';
		recognition.interimResults = false;
		recognition.maxAlternatives = 1;

		document.getElementById('play')!.onclick = function () {
			recognition.start();
			result = false;
			console.log('Ready to receive a color command.');
		};

		recognition.onresult = function (event: any) {
			error = '';
			console.log({ event });
			for (let _res of event.results) {
				for (let alt of event.results) {
					if (riddle.answers.filter((item) => item === alt.transcript).length) {
						result = true;
						console.log('true')
						return;
					}
				}
			}
		};

		recognition.onspeechend = function () {
			recognition.stop();
		};

		recognition.onnomatch = function (event: any) {
			error = 'Nie rozpoznano wyrazu.';
		};

		recognition.onerror = function (event: any) {
			console.log({ event });
			error = event.error;
			result = false;
		};
	}
</script>

<div>
	<h1>Co to jest?</h1>
	<div class="center">
		<img src={riddle.imageUrl} alt={riddle.imageAlt} style:margin="auto" />
	</div>
	<div class="center">
		<Button id="play" text="Naciśnij, aby spróbować" />
	</div>
	<div>
		{#if result}
			<h3>Brawo!!! Udało się!</h3>
		{/if}
		{#if error}
			<p id="error"><em>Error: {error}</em></p>
		{/if}
	</div>
</div>

<style>
	h1 {
		display: block;
		text-align: center;
	}
	div > .center {
		display: block;
		width: fit-content;
		margin: auto;
	}
</style>
