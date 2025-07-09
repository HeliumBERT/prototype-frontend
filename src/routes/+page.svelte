<script lang="ts">
	import { Button, Form, Input, InputGroup } from '@sveltestrap/sveltestrap';
</script>


<div class="vh-100 w-100 position-fixed top-0 start-0 overflow-hidden">
	<div class="d-flex flex-column align-items-center position-absolute top-50 start-50 translate-middle w-100">
		<div class="d-flex flex-column align-items-center w-100">
			<div class="d-flex flex-column align-items-center">
				<h1>Sentiment Analysis</h1>
				<p>Analysis of the sentiment and classifying it as good or bad.</p>
			</div>

			<div class="d-flex flex-column align-items-center w-50">
				<Form class="w-100">
					<div class="d-flex flex-column align-items-center w-100">
						<Input placeholder="Enter text to analyze" class="mb-3 w-100" />
						<Button color="primary" class="mb-3 w-50">Analyze</Button>
					</div>
				</Form>
			</div>
		</div>
	</div>
</div>