<script>
  import { getContext, setContext } from "svelte";
  import SuperButton from "../../bb_super_components_shared/src/lib/SuperButton/SuperButton.svelte";
  import SuperPopover from "./../../bb_super_components_shared/src/lib/SuperPopover/SuperPopover.svelte";
  import { writable } from "svelte/store";
  const { styleable, builderStore, enrichButtonActions } = getContext("sdk");
  const component = getContext("component");
  const context = getContext("context");
  const parentMenu = getContext("super-menu");

  export let direction = "row";
  export let buttonClass;
  export let size;
  export let quiet;
  export let iconOnly;
  export let compact;
  export let disabled;
  export let collapsed;
  export let collapsedText;
  export let icon = "ri-arrow-down-s-line";
  export let iconFirst;

  export let buttons;
  export let align = "flex-end";

  let open = false;
  let anchor;
  let hovered;
  let childHovered = new writable(0);

  $: parentMenu?.set(open || hovered);
  $: nested =
    $component.ancestors.at(-2) == "plugin/bb-component-SuperButtonToolbar";

  setContext("super-menu", childHovered);
</script>

<div use:styleable={$component.styles}>
  {#if collapsed}
    <!-- svelte-ignore a11y-no-static-element-interactions -->
    <div class="wrapper" class:nested style:--justification={align}>
      <!-- svelte-ignore a11y-click-events-have-key-events -->
      <div
        bind:this={anchor}
        class="drop-menu"
        class:open
        class:small={size == "S"}
        class:large={size == "L"}
        class:nested
        on:click={() => (open = !open)}
      >
        {#if icon && iconFirst}
          <i class={icon} />
        {/if}
        {collapsedText}
        {#if (icon && !iconFirst) || nested}
          <i class={nested ? "ri-arrow-right-s-line" : icon} />
        {/if}
      </div>
    </div>
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
            onClick={enrichButtonActions(button.onClick, $context)}
          />
        {/each}
      {:else if $builderStore.inBuilder && $component.children < 1}
        <span class="empty">Please Define Some Buttons </span>
      {/if}
      <slot />
    </div>
  {/if}
</div>

{#if open || $childHovered}
  <SuperPopover
    {anchor}
    open
    align={nested ? "right-outside" : align == "flex-start" ? "left" : "right"}
    className={"super-menu"}
    ignoreAnchor={false}
    on:close={() => {
      open = false;
      $childHovered = false;
    }}
  >
    <!-- svelte-ignore a11y-no-static-element-interactions -->
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
            menuAlign={align == "flex-start" ? "left" : "right"}
            iconAfterText={align != "flex-start"}
            onClick={enrichButtonActions(button.onClick, $context)}
            on:click={() => (open = false)}
          />
        {/each}
      {/if}
      <slot />
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
      flex: none !important;
      flex-direction: column;
    }
  }

  .spectrum-ActionGroup--compact {
    gap: 0px;
  }

  .wrapper {
    flex: auto;
    display: flex;
    justify-content: var(--justification);

    &.nested {
      justify-content: stretch;
    }
  }

  .empty {
    min-height: 2.5rem;
    display: flex;
    align-items: center;
    padding-left: 1rem;
    padding-right: 1rem;
  }

  .button-list {
    padding: 0.5rem;
    display: flex;
    flex-direction: column;
    align-items: stretch;
    min-width: 180px;
  }

  .drop-menu {
    min-height: 2rem;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.5rem;
    padding: 0rem 1rem;
    transition: 230;
    opacity: 0.75;

    &.small {
      min-height: 1.75rem;
    }

    &.large {
      min-height: 2.4rem;
    }

    &.nested {
      flex: auto;
      justify-content: space-between;
      padding-left: 0.75rem;
      padding-right: 0.75rem;
    }
    &.open {
      color: var(--spectrum-global-color-gray-900);
      background-color: var(--spectrum-global-color-gray-200);
      opacity: 1;
    }

    &:hover {
      background-color: var(--spectrum-global-color-gray-200);
      opacity: 1;
    }
  }

  :global(.super-menu) {
    box-shadow: unset !important;
    filter: unset !important;
  }
</style>
