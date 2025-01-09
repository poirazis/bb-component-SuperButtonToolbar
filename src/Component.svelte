<script>

  import { getContext, onMount } from "svelte";
  import SuperButton from "../../bb_super_components_shared/src/lib/SuperButton/SuperButton.svelte";
	import SuperPopover from './../../bb_super_components_shared/src/lib/SuperPopover/SuperPopover.svelte';
  import Portal from "svelte-portal";

	const { styleable, builderStore, enrichButtonActions } = getContext("sdk");
  const component = getContext("component");
  const context = getContext("context");


  export let direction = "row";
  export let buttonClass;
  export let size;
  export let quiet;
  export let iconOnly;
  export let compact;
  export let disabled;
  export let inNavBar;
  export let hidePortal
  export let collapsed
  export let collapsedText
  export let collapsedIcon


  export let makeCollapsible

  export let buttons;
  export let align = "flex-end";

  let open = false
  let anchor

	let leftNav

  onMount( () => {
    setTimeout( () =>  {leftNav = document.getElementsByClassName("nav--left").length }, 100);
  })

</script>

{#if inNavBar}
  <Portal target={ leftNav ? ".nav" : ".logo"}>
    <div use:styleable={$component.styles}>
      <div
        class="spectrum-ActionGroup spectrum-ActionGroup--size{size}"
        class:vertical={direction == "column"}
        class:spectrum-ActionGroup--quiet={quiet}
        class:spectrum-ActionGroup--compact={compact}
        style:--justification={align}
        style:justify-self={"flex-end"}
      >
      {#if leftNav}
        {@html "<style>  .links { flex : auto !important } </style>" }
      {/if}

      {#if makeCollapsible}
        {@html "<style>  .nav--left { max-width: 3rem !important } </style>" }
      {/if}

      {#if hidePortal}
        {@html "<style>  .portal { display : none !important } </style>" }
      {/if}

        {#if buttons?.length}
          {#each buttons as { icon ,quiet, text, type , size, onClick }}
            <SuperButton
              {buttonClass}
              {quiet}
              {iconOnly}
              {text}
              {type}
              {size}
              {icon}
              onClick={enrichButtonActions(onClick, $context )}
            />
          {/each}
        {:else if $builderStore.inBuilder && $component.children < 1}
          <span class="empty">Please Define Some Buttons </span>
        {/if}
        <slot />
      </div>
    </div>
  </Portal>
{:else}
  <div use:styleable={$component.styles}>
    {#if collapsed}
      <SuperButton
        {buttonClass}
        {quiet}
        {iconOnly}
        {size}
        {disabled}
        iconAfterText
        text={collapsedText}
        icon={"ri-arrow-down-s-line"}
        on:click={(e) => {open = !open; if (!anchor) anchor = e.detail; }}
      />
    {:else}
    <div
      class="spectrum-ActionGroup spectrum-ActionGroup--size{size}"
      class:vertical={direction == "column"}
      class:spectrum-ActionGroup--quiet={quiet}
      class:spectrum-ActionGroup--compact={compact}
      style:--justification={align}
    >
      {#if buttons?.length}
        {#each buttons as button}
          <SuperButton
            {buttonClass}
            {quiet}
            {iconOnly}
            {...button}
            {size}
            {disabled}
            onClick={enrichButtonActions(button.onClick, $context )}
          />
        {/each}
      {:else if $builderStore.inBuilder && $component.children < 1}
        <span class="empty">Please Define Some Buttons </span>
      {/if}
      <slot />
    </div>
    {/if}
  </div>

{/if}

{#if open && buttons?.length}
  <SuperPopover {anchor} open align="right">
		<div class="button-list">
			{#if buttons?.length}
    	  {#each buttons as button}
				  <SuperButton
					  {buttonClass}
					  {quiet}
					  {iconOnly}
					  {...button}
					  {size}
					  {disabled}
					  menuItem
					  onClick={enrichButtonActions(button.onClick, $context )}
            on:click={() => open = false}
				  />
  		  {/each}
		  {/if}
    </div>
  </SuperPopover>
{/if}

<style>
  .spectrum-ActionGroup {
    display: flex;
    margin-top: 0px !important;
    gap: 0.25rem;
    justify-content: var(--justification);

    &.vertical {
      flex: none;
      flex-direction: column;
    }
  }

  .spectrum-ActionGroup--compact {
    gap: 0px;
  }

  .empty {
    min-height: 2.5rem;
    display: flex;
    align-items: center;
    padding-left: 1rem;
  }

	.button-list {
    padding: 0.5rem;
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 0.25rem;
		min-width: 180px;
	}
</style>
