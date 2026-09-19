<script lang="ts">
	import type { Database } from '$lib/database.types';
	import { supabase } from '$lib/supabaseClient';
	import { onMount } from 'svelte';

	let guestbook: Database['public']['Tables']['guests']['Row'][] | undefined =
		$state.raw(undefined);
	let new_message: Database['public']['Tables']['guests']['Insert'] = $state({
		message: '',
		user_name: '',
	});

	onMount(async () => {
		const {
			data: { session },
		} = await supabase.auth.getSession();
		if (session === null) {
			await supabase.auth.signInAnonymously();
		}
		const supabase_response = await supabase.from('guests').select().order('created_at');
		guestbook = supabase_response.data ?? [];
	});
</script>

<h3>Guestbook</h3>
<ul>
	{#each guestbook as guestbook_entry}
		<li><b>{guestbook_entry.user_name}</b>: {guestbook_entry.message}</li>
	{/each}
</ul>

<div>
	<input style="display: block;" type="text" bind:value={new_message.user_name} />
	<textarea bind:value={new_message.message}></textarea>
</div>
