<script lang="ts">
	import { Navbar, NavbarBrand, Button, Form, FormGroup, Input, Modal, ModalBody, Container, Icon, Spinner, Label } from '@sveltestrap/sveltestrap';
	import { z } from 'zod';
	import { onMount } from 'svelte';
	import { page } from '$app/state';
	import PasswordFormPart from '$lib/components/formParts/passwordFormPart.svelte';
	import { goto } from '$app/navigation';


	let username = $state("");
	let passwordFp: PasswordFormPart;

	let showModal = $state(false);

	let loggingIn = $state(false);


	const loginSchema = z.object({
		username: z.string().nonempty()
	})

	async function login() {
		goto("./portal/dashboard");
		// let loginResult: { username: string; password: string; };
		// try {
		// 	const usernameParsed = await loginSchema.parseAsync({
		// 		username: username
		// 	})

		// 	loginResult = {
		// 		...usernameParsed,
		// 		password: passwordFp.getResult()
		// 	}
		// }
		// catch (e: unknown) {
		// 	if (handlePossibleZodError(e)) return;
		// 	throw e;
		// }

		// loggingIn = true;
		// const result = await makeLoginRequest(loginResult);
		// loggingIn = false;

		// if (typeof result !== "boolean") {
		// 	alert(`Too many requests. Please try again later in ${result.retryAfter !== null ? `${result.retryAfter}s` : "a few moments"}.`);
		// 	return;
		// }

		// if (!result) {
		// 	alert("The username or password is incorrect.");
		// 	return;
		// }

		// goto('./portal/dashboard');
	}

	onMount(() => {
        if (page.url.searchParams.get('openLogin') === 'true') {
			showModal = true;
        }
	});
</script>

<!-- Fixed Background & Centered Content -->
<div class="vh-100 w-100 position-fixed top-0 start-0 overflow-hidden">
	<div
		class="position-absolute top-50 start-50 translate-middle text-white text-center"
	>
		<div class="d-flex flex-column bg-dark bg-opacity-75 p-4 rounded">
			<h1 class="display-4 text-warning">BOARD DOCUMENTS DATABASE</h1>
			<hr class="border border-warning w-100 my-2" style="height: 3px;" />
			<p class="lead text-warning">A Document Searching System</p>
		</div>

		<div class="d-flex mt-2 w-100">
			<Button color="warning" class="m-0 w-100 shadow border-5" on:click={() => (showModal = true)}>
				LOG IN
			</Button>
		</div>
	</div>
</div>

<!-- Login Modal -->
<Modal bind:isOpen={showModal}>
	<ModalBody class="text-center position-relative">
		<Button
			color="white"
			class="position-absolute top-0 end-0 m-2 p-0 border-0"
			on:click={() => (showModal = false)}
		>
			<Icon name="x" class="h1" />
		</Button>

		<Icon name="person-circle" class="fs-1" />

		<div class="mt-2 mb-3">
			<h3 class="bold">LOG IN</h3>
		</div>

		<Form>
			<FormGroup>
				<Label for="username">Username</Label>
				<Input type="text" id="username" bind:value={username} required placeholder="Username" />
			</FormGroup>

			<PasswordFormPart toFor="password" performValidation={false} bind:this={passwordFp} />

			<Button color="dark" class="w-50 mt-3" on:click={login}>
				<div class="d-flex justify-content-center w-100">
					{#if loggingIn}
						<div class="d-flex flex-row align-items-center">
							<Spinner color="secondary" size="sm" />
							<span class="ms-3">Logging in...</span>
						</div>
					{:else}
						Log In
					{/if}
				</div>
			</Button>
		</Form>
	</ModalBody>
</Modal>
