<script lang="ts">
	import { store, clearActiveFilters } from "@/store.svelte";
	import type { Filters } from "@/types";
	import Icon from "../Icon.svelte";
	import tooltip from "../actions/tooltip";
	import Menu from "../Menu.svelte";
  	import RangeSlider from 'svelte-range-slider-pips';

	function filterClicked(type: keyof Filters, f: string) {
		if (store.activeFilters[type]?.includes(f)) {
			store.activeFilters[type] = store.activeFilters[type]?.filter(
				(a) => a !== f,
			);
		} else {
			store.activeFilters[type] = [...store.activeFilters[type], f];
		}
		store.activeFilters = store.activeFilters;
		window.scrollTo({ top: 0 });
	}

	// Local state for range slider
    let raterange = $state([...(store.activeFilters.rating ?? [0, 10])]);

    // Update local state when store changes
    $effect(() => {
        raterange = [...(store.activeFilters.rating ?? [0, 10])];
    });

</script>

<Menu conf={{ width: "200px", right: "47px", arrowLeft: "38px" }}>
	<div class="title">
		<h4 class="norm sm-caps">type</h4>
		{#if store.activeFilters?.type?.length > 0 || store.activeFilters?.status?.length > 0}
			<button
				class="plain"
				use:tooltip={{ text: "Clear", pos: "left" }}
				onclick={() => {
					clearActiveFilters();
					window.scrollTo({ top: 0 });
				}}
			>
				<Icon i="close-circle" wh={18} />
			</button>
		{/if}
	</div>
	<div class="type-filter">
		<button
			class:active={store.activeFilters.type.includes("tv")}
			onclick={() => filterClicked("type", "tv")}
		>
			SHOW
		</button>
		<button
			class:active={store.activeFilters.type.includes("movie")}
			onclick={() => filterClicked("type", "movie")}
		>
			MOVIE
		</button>
		{#if store.serverFeatures?.games}
			<button
				class:active={store.activeFilters.type.includes("game")}
				onclick={() => filterClicked("type", "game")}
			>
				GAME
			</button>
		{/if}
	</div>
	<h4 class="norm sm-caps">status</h4>
	<button
		class={`plain ${store.activeFilters.status.includes("planned") ? "on" : ""}`}
		onclick={() => filterClicked("status", "planned")}
	>
		planned
	</button>
	<button
		class={`plain ${store.activeFilters.status.includes("watching") ? "on" : ""}`}
		onclick={() => filterClicked("status", "watching")}
	>
		watching
		{#if store.serverFeatures?.games}
			(playing)
		{/if}
	</button>
	<button
		class={`plain ${store.activeFilters.status.includes("finished") ? "on" : ""}`}
		onclick={() => filterClicked("status", "finished")}
	>
		finished
		{#if store.serverFeatures?.games}
			(played)
		{/if}
	</button>
	<button
		class={`plain ${store.activeFilters.status.includes("hold") ? "on" : ""}`}
		onclick={() => filterClicked("status", "hold")}
	>
		on hold
	</button>
	<button
		class={`plain ${store.activeFilters.status.includes("dropped") ? "on" : ""}`}
		onclick={() => filterClicked("status", "dropped")}
	>
		dropped
	</button>
	<h4 class="norm sm-caps">my list</h4>
	<h4 class="norm sm-caps">rating</h4>
	<div class="slider-wrapper">

		<RangeSlider 
			id="ratingrange"
			class="handle-triangle track-size float-style pips-bottom pips-style"
			min={0}
			step={1}
			max={10}
			values={raterange}
			pips
			all="label"
			rest="pip"
			range
			float
			rangeFloat
			on:change={(e) => { 
				raterange = [...e.detail.values];
				store.activeFilters = { ...store.activeFilters, rating: [...e.detail.values] };
			}
			}
		/>
	</div>

</Menu>

<style lang="scss">
	h4:not(:first-child) {
		margin-top: 8px;
		margin-bottom: 5px;
	}

	.title {
		display: flex;
		flex-flow: row;
		align-items: center;
		margin-bottom: 8px;
		gap: 5px;
		/* Always height of when clear filters btn is shown so there is no jump */
		min-height: 26px;

		button.plain {
			display: flex;
			align-items: center;
			justify-content: center;
			width: 28px;
			height: 26px;
			padding: 2px 3px;
			border-radius: 8px;

			&:first-of-type {
				margin-left: auto;
			}
		}
	}

	button.plain {
		text-transform: capitalize;
		position: relative;

		&.on::before {
			content: "\2713";
		}

		&::before {
			position: absolute;
			top: 4px;
			left: 7.5px;
			font-family:
				system-ui,
				-apple-system,
				BlinkMacSystemFont;
			font-size: 18px;
		}
	}

	.type-filter {
		display: flex;
		flex-flow: row;
		flex-wrap: wrap;
		gap: 3px;
		width: 100%;

		button {
			flex: 1 1 45%;
			padding: 8px 0;
			border-radius: 10px;
		}
	}

	.slider-wrapper {
		width: 100%;
		padding: 0px 6px 6px 0px;
		box-sizing: border-box;
	}

	:global(.rangeSlider) {
		display: block;
		width: calc(100% - 22px);
		max-width: calc(100% - 22px);
		box-sizing: border-box;
		margin: 0 auto;
		padding: 0;
		line-height: 1;
		vertical-align: middle;
		--range-slider: var(--bg-color);
		--slider-accent: var(--text-color);
		--slider-base: var(--text-color-accent);
		--slider-bg: var(--bg-color);
		--range-range: var(--text-color);
		--range-handle: var(--text-color);
		--range-handle-focus: var(--text-color);
		--range-handle-border: var(--text-color);
		--range-float: var(--text-color);
		--range-float-text: 	font-size: 30px;
		--range-float-text:		var(--bg-color);
		--range-range-inactive: var(--bg-color-accent);
		--range-range-hover: var(--text-color);
		--range-range-press: var(--text-color);
		--range-pip: var(--text-color-accent);
		--range-pip-active: var(--text-color);
		--range-pip-in-range: var(--text-color);
		--range-pip-out-of-limit: var(--text-color-accent);
		--range-pip-hover: var(--text-color);
		--range-pip-text: var(--text-color);
		--range-pip-active-text: var(--bg-color);
		--range-pip-in-range-text: var(--bg-color);
		--range-pip-hover-text: var(--bg-color);
		  /* custom aesthetics */
  --track-width: 0.6em;
  --track-radius: 1;
  --track-padding: 0.3em;
  --range-width: 0.6em;
  --range-radius: 1;
  --range-padding: 0em;
 
  --handle-offset: 0.1em;
  --handle-offset-block: 0em;
  --handle-rotate: 0deg;
  --handle-size: 1.2em;
 
  --float-offset: 5%;
  --float-offset-inline: -0.1em;
  --range-float-offset: 5%;
  --range-float-offset-inline: 0em;
  --float-size: 1.2em;
  --float-padding: 0.4em;
  --float-radius: 0.5;
 
  --pips-offset: 25%;
  --pips-height: 0.6em;
  --pip-selected-offset: -5%;
  --pip-selected-height: 0.7em;
  --pip-inrange-offset: -10%;
  --pip-inrange-height: 0.6em;
  --pip-val-size: 0.9em;
  --pip-val-offset: -0.3em;
	}

</style>
