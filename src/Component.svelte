<script>
  import { getContext, setContext } from "svelte";
  import { SuperButton, SuperPopover } from "@poirazis/supercomponents-shared";
  import { writable } from "svelte/store";
  const { styleable, builderStore, enrichButtonActions, screenStore } =
    getContext("sdk");
  const component = getContext("component");
  const context = getContext("context");
  const parentMenu = getContext("super-menu");
  const parentMenuAlign = getContext("super-menu-align");

  export let direction = "row";
  export let buttonClass;
  export let size;
  export let quiet;
  export let iconOnly;
  export let compact;
  export let disabled;
  export let collapsed;
  export let collapsedText;
  export let icon;
  export let iconFirst;

  export let buttons;
  export let align = "flex-end";

  let open = false;
  let anchor;
  let hovered;
  let childHovered = new writable(0);

  const lookupComponent = (components, id) => {
    let parent;
    let pos = components?.findIndex((comp) => comp._id == id);
    if (pos > -1) return components[pos];
    else
      components?.forEach((comp) => {
        if (!parent) parent = lookupComponent(comp._children, id);
      });

    return parent;
  };

  const parent = lookupComponent(
    $screenStore.activeScreen.props._children,
    $component.path.at(-2)
  );

  const nested = parent?._component == "plugin/bb-component-SuperButtonToolbar";
  $: align = nested ? parentMenuAlign : align;
  $: open = $component.inSelectedPath && !$component.selected && collapsed;
  $: icon = nested
    ? parentMenuAlign == "flex-end"
      ? "ri-arrow-left-s-line"
      : "ri-arrow-right-s-line"
    : icon;

  $: iconFirst = nested
    ? parentMenuAlign == "flex-end"
      ? true
      : false
    : iconFirst;

  $: parentMenu?.set(open || hovered);

  $: $component.styles = {
    ...$component.styles,
    normal: {
      ...$component.styles.normal,
      "max-width": nested ? "100%" : "fit-content",
    },
  };

  setContext("super-menu", childHovered);
  $: setContext("super-menu-align", align);
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
        {#if icon && !iconFirst}
          <i class={icon} />
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
    {open}
    align={align == "flex-start"
      ? nested
        ? parentMenuAlign == "flex-start"
          ? "right-outside"
          : "left-outside"
        : "left"
      : nested
        ? parentMenuAlign == "flex-end"
          ? "left-outside"
          : "right-outside"
        : "right"}
    className={"super-menu"}
    topOffset={0}
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
            quiet={true}
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
    min-width: 160px;
    gap: 0.25rem;
    background-color: var(--spectrum-global-color-gray-50);
  }

  .drop-menu {
    min-height: 2rem;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.5rem;
    padding: 0rem 0.75rem;
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
      color: var(--spectrum-global-color-gray-900);
      background-color: var(--spectrum-global-color-gray-200);
      opacity: 1;
      cursor: pointer;
    }
  }

  :global(.super-menu) {
    filter: unset !important;
    box-shadow:
      0px 0px 0px rgba(3, 7, 18, 0.1),
      1px 1px 1px rgba(3, 7, 18, 0.08),
      1px 3px 3px rgba(3, 7, 18, 0.06),
      3px 5px 5px rgba(3, 7, 18, 0.04),
      4px 8px 8px rgba(3, 7, 18, 0.02) !important;
  }
</style>
