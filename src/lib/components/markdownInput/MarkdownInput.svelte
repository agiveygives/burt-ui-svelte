<script lang="ts">
	export let blogId: string;
	export let blogData: BlogType = {
		description: '',
		authors: '',
		tags: [],
		title: 'New Blog Post',
		content: ''
	};

	import { goto } from '$app/navigation';
	import classnames from 'classnames';
	import Button from '@components/button';
	import Drawer from '@components/drawer';
	import { TextInput } from '@components/input';
	import MetadataInput from '@components/markdownInput/MetadataInput.svelte';
	import TitleInput from '@components/markdownInput/TitleInput.svelte';
	import Controls from '@components/markdownInput/Controls.svelte';
	import RenderMarkdown from '@components/renderMarkdown';
	import type BlogType from '@/types/blogType';
	import markdownData from '@components/markdownInput/store';

	markdownData.set(blogData);

	let innerWidth = 0;

	let description: string = $markdownData.description;
	let authors: string = $markdownData.authors;
	let tags: string[] = $markdownData.tags;
	let blogTitle = $markdownData.title;
	let textareaRef: HTMLElement;
	let isPreview = false;

	const publish = (isPublic: boolean) => {
		const uri = blogId ? `/api/blog/${blogId}` : '/api/blog';

		fetch(uri, {
			method: 'post',
			body: JSON.stringify({
				title: blogTitle,
				description,
				authors,
				tags,
				content: $markdownData.content,
				draft: !isPublic
			})
		})
			.then((res) => res.json())
			.then((body) => {
				goto(`/blog/${body.id}`);
			})
			.catch((error) => {
				console.log(error);
			});
	};
</script>

<svelte:window bind:innerWidth />

<div class="container">
	{#if innerWidth > 0}
		<div class="md-input-container">
			<TitleInput />

			<div class={classnames('controls', { hidden: isPreview })}>
				<Controls {textareaRef} text={$markdownData.content} />
			</div>

			{#if isPreview}
				<RenderMarkdown markdown={$markdownData.content} />
			{:else}
				<textarea bind:this={textareaRef} bind:value={$markdownData.content} />
			{/if}
		</div>

		{#if innerWidth < 1200}
			<Drawer placement="right" size="300px">
				<div class="drawer-content">
					<MetadataInput
						bind:isPreview
						onPublish={() => publish(true)}
						onSaveDraft={() => publish(false)}
					/>
				</div>
			</Drawer>
		{:else}
			<aside>
				<MetadataInput
					bind:isPreview
					onPublish={() => publish(true)}
					onSaveDraft={() => publish(false)}
				/>
			</aside>
		{/if}
	{/if}
</div>

<style>
	.hidden {
		display: none;
		visibility: hidden;
	}

	.container {
		flex-grow: 1;
		height: auto;
		display: grid;
		grid-template-columns: 800px auto;
		gap: 50px;
		justify-items: start;
		justify-content: center;
	}

	@media screen and (max-width: 1200px) {
		.container {
			grid-template-columns: 80vw;
		}
	}

	.md-input-container {
		display: flex;
		width: 100%;
		flex-direction: column;
	}

	.controls {
		display: grid;
		justify-content: start;
		grid-template-columns: auto auto;
		align-items: center;
		max-width: 100vw;
	}

	textarea {
		height: 100%;
		resize: none;
		padding: 20px;
		border-radius: 5px;
		border: solid 1px var(--mint);
	}

	textarea:focus,
	textarea:focus-visible {
		border: solid 1px var(--caribbean-current);
		outline: none;
	}

	aside {
		position: sticky;
		top: 105px;
		align-self: start;
		display: grid;
		gap: 15px;
	}

	.drawer-content {
		display: flex;
		flex-direction: column;
		gap: 15px;
		padding: 20px;
	}
</style>
