<script>
	import { toast } from 'svelte-sonner';
	import { goto } from '$app/navigation';

	import { onMount, getContext } from 'svelte';
	const i18n = getContext('i18n');

	import { page } from '$app/stores';
	import { config, models, settings } from '$lib/stores';

	import { getModelById, updateModelById } from '$lib/apis/models';

	import { getModels } from '$lib/apis';


	let model = null;

	onMount(async () => {
		const _id = $page.url.searchParams.get('id');
		if (_id) {
			model = await getModelById(localStorage.token, _id).catch((e) => {
				return null;
			});

			if (!model) {
				goto('/workspace/models');
			}
		} else {
			goto('/workspace/models');
		}
	});

	const onSubmit = async (modelInfo) => {
		const res = await updateModelById(localStorage.token, modelInfo.id, modelInfo);

		if (res) {
			await models.set(
				await getModels(
					localStorage.token,
					$config?.features?.enable_direct_connections && ($settings?.directConnections ?? null)
				)
			);
			toast.success($i18n.t('Model updated successfully'));
			await goto('/workspace/models');
		}
	};
</script>

{#if model}
	<div class="flex flex-col items-center justify-center w-full h-64">
		<div class="text-gray-500 dark:text-gray-400 text-lg mb-4">
			{$i18n.t('Model editing functionality has been removed')}
		</div>
		<button
			class="px-4 py-2 bg-gray-100 hover:bg-gray-200 dark:bg-gray-800 dark:hover:bg-gray-700 rounded-lg"
			on:click={() => goto('/workspace/models')}
		>
			{$i18n.t('Back to Models')}
		</button>
	</div>
{/if}
