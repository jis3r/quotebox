<script>
	import { createEventDispatcher } from 'svelte';
	import { addQuote } from '../../stores/quoteStore';
	import { supabaseClient } from '$lib/supabase';

	const dispatch = createEventDispatcher();

	let user_id = '';
	let date = new Date().toISOString().slice(0, 10);
	let text = '';
	let author = '';
	/**
	 * @type {string[]}
	 */
	let tags = [];

	async function getUser() {
		const {
			data: { user }
		} = await supabaseClient.auth.getUser();
		if (user) {
			user_id = user.id;
			console.log(user);
		} else {
			console.log('User not logged in.');
		}
	}

	getUser();

	function handleSubmit(event) {
		event.preventDefault();
		const selectedDate = new Date(date);
		const currentDate = new Date();
		const newQuote = {
			created_at: new Date(
				selectedDate.setHours(
					currentDate.getHours(),
					currentDate.getMinutes(),
					currentDate.getSeconds(),
					currentDate.getMilliseconds()
				)
			).toISOString(),
			text,
			author,
			tags
		};
		console.log(typeof newQuote.created_at);
		addQuote(user_id, newQuote);
		dispatch('addQuote', newQuote);
		text = '';
		author = '';
		date = new Date().toISOString().slice(0, 10);
	}

	function handleCancel() {
		dispatch('cancel');
	}
</script>

<div class="mx-auto max-w-md rounded-md bg-gray-50 p-6 shadow-md dark:bg-gray-800">
	<h2 class="mb-4 text-xl font-semibold">Add Quote</h2>
	<form on:submit={handleSubmit}>
		<div class="mb-4">
			<label for="quote" class="mb-2 block font-medium">Quote:</label>
			<textarea
				id="quote"
				class="w-full rounded-md border border-gray-300 bg-gray-50 p-2 focus:border-svelte-orange focus:outline-none dark:bg-gray-800"
				bind:value={text}
				required
			/>
		</div>

		<div class="mb-4">
			<label for="author" class="mb-2 block font-medium">Author:</label>
			<input
				type="text"
				id="author"
				class="w-full rounded-md border border-gray-300 bg-gray-50 p-2 focus:border-svelte-orange focus:outline-none dark:bg-gray-800"
				bind:value={author}
				required
			/>
		</div>

		<div class="mb-4">
			<label for="date" class="mb-2 block font-medium">Date:</label>
			<input
				type="date"
				id="date"
				class="w-full rounded-md border border-gray-300 bg-gray-50 p-2 focus:border-svelte-orange focus:outline-none dark:bg-gray-800"
				bind:value={date}
			/>
		</div>

		<div class="flex justify-between">
			<button
				type="button"
				class="rounded-full border border-gray-1000 py-2 px-4 font-medium text-gray-1000 dark:border-gray-50 dark:text-gray-50"
				on:click={handleCancel}
			>
				Cancel
			</button>
			<button
				type="submit"
				class="rounded-full bg-gray-1000 py-2 px-4 font-medium text-gray-100 dark:bg-gray-50 dark:text-gray-1000"
			>
				Add Quote
			</button>
		</div>
	</form>
</div>
