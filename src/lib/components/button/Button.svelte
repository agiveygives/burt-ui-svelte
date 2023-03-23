<script lang="ts">
	export let color: 'primary' | 'secondary' = 'primary'
	export let variant: 'filled' | 'outlined' | 'text' = 'filled';
	export let rounded = false;

	import classnames from 'classnames';

	let buttonClasses = variant;

	$: buttonClasses = classnames(color, variant, {
		rounded: rounded
	});
</script>

<button class={buttonClasses} on:click>
	<slot />
</button>

<style>
	button {
		height: fit-content;
		border: none;
		border-radius: 5px;
		font-size: 16px;
		padding: 5px 0px;
		transition: 0.3s ease-in-out;
		cursor: pointer;
		padding: 5px 10px;
	}

	.rounded {
		border-radius: 15px;
	}

	button:hover,
	button:focus {
		filter: contrast(50%);
	}

	.filled .primary {
		background-color: var(--button--primary--background-color);
		color: var(--button--primary--text-color);
	}

	.filled .secondary {
		background-color: var(--button--secondary--background-color);
		color: var(--button--secondary--text-color);
	}

	.outlined .primary {
		border: solid 2px var(--button--primary--background-color);
		color: var(--button--primary--background-color);
	}

	.outlined .secondary {
		border: solid 2px var(--button--secondary--background-color);
		color: var(--button--secondary--background-color);
	}

	.text .primary {
		background-color: transparent;
		color: var(--button--primary--background-color);
	}

	.text:hover .primary,
	.text:focus .primary {
		text-shadow: 0px 0px 1px var(--button--primary--background-color);
	}

	.text .secondary {
		background-color: transparent;
		color: var(--button--secondary--background-color);
	}

	.text:hover .primary,
	.text:focus .primary {
		text-shadow: 0px 0px 1px var(--button--secondary--background-color);
	}
</style>
