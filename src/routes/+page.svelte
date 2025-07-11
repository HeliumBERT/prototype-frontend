<script lang="ts">
	import { Accordion, AccordionItem, Alert, Button, Form, Input, InputGroup, Spinner } from '@sveltestrap/sveltestrap';
	import { env } from '$lib/env';
	import { slide } from 'svelte/transition';
	import { cubicOut } from 'svelte/easing';
	

	interface SentimentAnalysisResult {
		confidence: number;
		elapsed_s: number;
		pred: number;
		probs: {
			LABEL_0: number;
			LABEL_1: number;
		};
	}

	let isLoading: boolean = $state(false);
	let currentSentimentResult: SentimentAnalysisResult | null = $state(null);
	let sentimentText: string = $state("");

	function toPercentage(value: number): string {
		return (value * 100).toFixed(2) + '%';
	}

	async function performSentimentAnalysis() {
		isLoading = true;

		let response: Response;
		try {
			response = await fetch(`${env.BACKEND_API_URL}/sentiment`, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify({ text: sentimentText })
			});

			if (!response.ok) {
				throw new Error(`HTTP error! status: ${response.status}`);
			}
		} catch (error) {
			console.error('Error during sentiment analysis:', error);
			currentSentimentResult = null;
			isLoading = false;
			return;
		}

		currentSentimentResult = await response.json() as SentimentAnalysisResult;

		isLoading = false;
	}
</script>


<div class="d-flex flex-column w-100 h-100">
	<div class="d-flex flex-column align-items-center justify-content-center w-100 h-100 overflow-auto">
		<div class="d-flex flex-column align-items-center w-100">
			<div class="d-flex flex-column align-items-center mb-2 w-100">
				<h1>Sentiment Analysis</h1>
				<h5>Classifying a statement as positive or negative.</h5>
				<h5>Brought to you by <u>HeliumBERT</u>, a lightweight AI model.</h5>
				<Accordion theme="dark" class="w-50 shadow">
					<AccordionItem header="More information">
						<div class="text-wrap text-break">
							<p class="m-0">
								Trained on wikitext + IMDB. Distilled from ALBERT. This means that:
							</p>
							<ul>
								<li>This model performs fast and is smaller compared to ALBERT-base.</li>
								<li>Due to using the IMDB dataset, the model predicts accurately <b>only when the text is a review on something</b> (ex. "This thing is good!").</li>
								<li>To provide more accurate results, the text should <b>only have alphanumeric characters and spaces, and should be a full sentence with punctuation</b>.</li>
							</ul>
						</div>
					</AccordionItem>
				</Accordion>
			</div>

			<div class="d-flex flex-column align-items-center w-50">
				<div class="d-flex flex-column align-items-center w-100">
					<Form class="w-100" on:submit={performSentimentAnalysis}>
						<InputGroup class="mb-3 w-100 shadow-lg">
							<Input
								placeholder="Enter text to analyze"
								disabled={isLoading}
								bind:value={sentimentText}
							/>
							<Button
								class="m-0"
								color="primary"
								role="submit"
								disabled={isLoading || sentimentText.trim() === ""}
							>
								{#if isLoading}
									<Spinner size="sm" color="white" />
								{:else}
									Analyze
								{/if}
							</Button>
						</InputGroup>
					</Form>

					{#if currentSentimentResult && !isLoading}
						<div class="d-flex flex-column" transition:slide={{ duration: 500, axis: 'y', easing: cubicOut }}>
							<Alert class="d-flex flex-column align-items-center" color={currentSentimentResult.pred === 0 ? 'danger' : 'success'}>
								<div class="d-flex flex-row justify-content-center align-items-center">
									<div class="d-flex flex-column w-75 align-items-center justify-content-center">
										<div class="d-flex flex-column align-items-center">
											<h5 class="text-white">Prediction:</h5>
											<h2 class="text-white">
												<b>{ currentSentimentResult.pred === 0 ? 'NEGATIVE' : 'POSITIVE' }</b>
											</h2>

											<h5 class="text-white">
												<i>Confidence: { toPercentage(currentSentimentResult.confidence) }</i>
											</h5>
										</div>

										<p class="m-0 text-white">
											<i>Elapsed Time: { currentSentimentResult.elapsed_s.toFixed(3) } seconds</i>
										</p>
									</div>

									<div class="d-flex flex-column align-items-center justify-content-center">
										<h5 class="text-center text-white">Confidence for all labels:</h5>
										<p class="m-0 text-white">
											Negative: {toPercentage(currentSentimentResult.probs.LABEL_0)}<br>
											Positive: {toPercentage(currentSentimentResult.probs.LABEL_1)}
										</p>
									</div>
								</div>
							</Alert>
						</div>
						
					{/if}
				</div>
			</div>
		</div>
	</div>


	<div class="d-flex flex-column w-100 text-center p-2 justify-content-center bg-dark">
		<p class="m-0 text-white">
			The model is not trained on its fullest due to hardware limitations, meaning that it is inaccurate on certain statements.<br>
			Made with 💖 by the HeliumBERT team.
		</p>
	</div>
</div>