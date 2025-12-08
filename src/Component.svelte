<script>
  import { getContext, setContext } from "svelte";
  import { SuperButton, SuperPopover } from "@poirazis/supercomponents-shared";
  import { writable } from "svelte/store";
  const {
    styleable,
    builderStore,
    enrichButtonActions,
    screenStore,
    processStringSync,
  } = getContext("sdk");
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
  export let iconSize = "M";
  export let iconColor;
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
  $: actuveButtons = buttons?.filter((btn) =>
    shouldShowButton(btn.conditions || [], $context)
  );
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

  function shouldShowButton(conditions, context) {
    function parseValue(val, typ) {
      switch (typ.toLowerCase()) {
        case "number":
          return Number(val);
        case "string":
          return String(val);
        case "boolean":
          return val === "true" || val === true;
        default:
          return val;
      }
    }

    function evaluateOperator(left, op, right) {
      switch (op.toLowerCase()) {
        case "equal":
        case "equals":
          return left == right; // loose for mixed types
        case "not equal":
        case "not equals":
          return left != right;
        case "greater than":
          return left > right;
        case "less than":
          return left < right;
        case "greater than or equal":
          return left >= right;
        case "less than or equal":
          return left <= right;
        case "contains":
          return typeof left === "string" && left.includes(right);
        case "not contains":
          return typeof left === "string" && !left.includes(right);
        case "starts with":
          return typeof left === "string" && left.startsWith(right);
        case "ends with":
          return typeof left === "string" && left.endsWith(right);
        case "is empty":
          return left == null || left === "";
        case "is not empty":
          return left != null && left !== "";
        default:
          return false;
      }
    }
    if (!conditions || conditions.length === 0) return true;
    const hasShow = conditions.some(
      (cond) => cond.action.toLowerCase() === "show"
    );
    let visible = !hasShow;
    for (const cond of conditions) {
      const refVal = processStringSync(cond.referenceValue, context);
      const newVal = processStringSync(cond.newValue, context);
      const parsedRef = parseValue(refVal, cond.type);
      const parsedNew = parseValue(newVal, cond.valueType);
      const matches = evaluateOperator(parsedRef, cond.operator, parsedNew);
      if (matches) {
        visible = cond.action.toLowerCase() === "show";
      }
    }
    return visible;
  }
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
          <i class={"ph ph-" + icon} class:icon-small={iconSize == "S"} class:icon-large={iconSize == "L"} style:color={iconColor} />
        {/if}
        {collapsedText}
        {#if icon && !iconFirst}
          <i class={"ph ph-" + icon} class:icon-small={iconSize == "S"} class:icon-large={iconSize == "L"} style:color={iconColor} />
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
      {#if actuveButtons?.length}
        {#each actuveButtons as button}
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
          {#if shouldShowButton(button.conditions)}
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
              onClick={() => {
                let cmd = enrichButtonActions(button.onClick, $context);
                cmd?.();
                open = false;
              }}
            />
          {/if}
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

  i.icon-small {
    font-size: 0.875rem;
  }

  i.icon-large {
    font-size: 1.5rem;
  }
</style>
