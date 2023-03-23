<script lang="ts">
	export let logo: string;
	export let logoAltText: string;

	export let navOptions: {
		href: string,
		display: string
	}[]

	export let menu: {
		icon: Element;
		options: {
			display: string;
			action: () => void;
		}[];
	}

	import classnames from 'classnames';
	import Popover from '@components/popover';
	import { clickOutside } from '@directives/onClickOutside';

	let isPopoverVisible = false;

	const togglePopover = () => {
		isPopoverVisible = !isPopoverVisible;
	};

	const closePopover = () => {
		isPopoverVisible = false;
	};

	let duration = '300ms';
	let headerClass = 'top';
	let y = 0;

	const deriveClass = (y: number) => {
		if (y <= 0) {
			return 'top';
		}

		return 'scrolled';
	};

	const setTransitionDuration = (node: HTMLElement) => {
		node.style.transitionDuration = duration;
	};

	$: headerClass = deriveClass(y);
</script>

<svelte:window bind:scrollY={y} />

<header use:setTransitionDuration class={headerClass}>
	<a href="/">
		<img src={logo} aria-label={logoAltText} alt={logoAltText} />
	</a>
	<nav>
		<ul>
			{#each navOptions as navOption}
				<li><a href={navOption.href}>{navOption.display}</a></li>
			{/each}
		</ul>
	</nav>

	{#if menu}
		<div use:clickOutside on:click_outside={closePopover}>
			<button class="menu" on:click={togglePopover}>{menu.icon}</button>
			<Popover show={isPopoverVisible}>
				{#each menu.options as option}
					<button class="menu-option" on:click={option.action}>{option.display}</button>
				{/each}
			</Popover>
		</div>
	{/if}

</header>

<style>
	header {
		position: fixed;
		z-index: 1;
		top: 0;
		right: 0;
		left: 0;
		display: grid;
		grid-template-columns: max-content auto max-content;
		grid-column-gap: 20px;
		align-items: center;
		padding: 5px 10px;
		transition: border-radius 300ms linear, background-color 300ms linear;
		background-color: var(--app-header--header--background-color);
	}

	header .top {
		border-radius: 0px;
	}

	header .scrolled {
		border-radius: 0px 0px 20px 20px;
		box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);
		background-color: var(--app-header--header--scrolled--background-color);
	}

	.menu {
		color: var(--app-header--menu--color);
		background: inherit;
		border-radius: 50px;
		padding: 8px 10px;
		border: unset;
		cursor: pointer;
	}

	.menu:hover {
		filter: contrast(50%);
	}

	img {
		height: 80px;
	}

	ul {
		display: grid;
		grid-template-columns: max-content max-content max-content;
		text-align: center;
		color: var(--app-header--nav--color);
		font-weight: 600;
		list-style: none;
		padding: 0px;
		margin: 0px;
	}

	li:not(:last-child) {
		padding-right: 10px;
		border-right: 1px solid var(--app-header--nav--color);
	}

	li:not(:first-child) {
		padding-left: 10px;
	}

	li a {
		color: var(--app-header--nav--color);
		text-decoration: none;
		display: block;
		position: relative;
		padding: 2px 0;
		text-decoration: none;
	}

	li a::after {
		content: '';
		position: absolute;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 0.1em;
		background-color: var(--app-header--nav--color);
		opacity: 0;
		transition: opacity 300ms, transform 300ms;
		opacity: 1;
		transform: scale(0);
		transform-origin: center;
	}

	li a:hover::after,
	li a:focus::after {
		opacity: 1;
		transform: translate3d(0, 0.2em, 0);
		transform: scale(1);
	}

	.menu-option {
		width: 100%;
		min-width: max-content;
		border: none;
		color: var(--app-header--menu--color);
		background-color: var(--white);
		font-size: 16px;
		padding: 10px 20px;
		cursor: pointer;
	}

	.menu-option:hover {
		filter: contrast(50%);
	}

	.menu-option:first-child {
		border-top-right-radius: 5px;
		border-top-left-radius: 5px;
	}

	.menu-option:last-child {
		border-bottom-right-radius: 5px;
		border-bottom-left-radius: 5px;
	}
</style>
