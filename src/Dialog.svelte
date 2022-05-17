<svelte:options tag="element-factory-md-dialog" />

<script>
  import { MDCDialog } from "@material/dialog";
  import { MDCRipple } from "@material/ripple";
  import { onMount } from "svelte";
  import 'material-icons';

  export let title = "";
  export let open = false;
  export let fullscreen = false;
  export let buttonoklabel = "OK";
  export let buttonokvariant = "raised";
  export let buttoncancelvariant;
  export let buttoncancellabel;
  export let dismissible = false;

  let element;
  let mdDialog;
  let dialog;

  onMount(() => {
    dialog = new MDCDialog(element.querySelector(".mdc-dialog"));
    const buttonRipple = new MDCRipple(element.querySelector(".mdc-button"));
  });

  function ok() {
    open = false;
  }
  function close() {
    open = false;
  }
  function cancel() {
    open = false;
  }
  function dismiss() {
    if (dismissible !== false) {
      open = false;
    }
  }
</script>

<main bind:this={element}>
  <div
    class="mdc-dialog globalCss"
    class:mdc-dialog--fullscreen={fullscreen}
    class:mdc-dialog--open={open}
  >
    <div class="mdc-dialog__container">
      <div
        class="mdc-dialog__surface"
        role="dialog"
        aria-modal="true"
        aria-labelledby="my-dialog-title"
        aria-describedby="dialog-content"
      >
        <div class="mdc-dialog__header">
          <h2 class="mdc-dialog__title" id="my-dialog-title">
            {title}
          </h2>
          {#if fullscreen || (!buttoncancellabel && !buttonoklabel)}
            <button
              class="mdc-icon-button mdc-dialog__close material-icons"
              data-mdc-dialog-action="close"
              on:click={close}
            >
              close
            </button>
          {/if}
        </div>
        <div class="mdc-dialog__content" id="dialog-content itemCss">
          <slot />
        </div>
        <div class="mdc-dialog__actions">
          {#if !!buttoncancellabel}
            <button
              tabindex="0"
              type="button"
              class="mdc-button mdc-dialog__button mdc-button--{buttoncancelvariant}"
              data-mdc-dialog-action="ok"
              on:click={cancel}
            >
              <div class="mdc-button__ripple" />
              <span class="mdc-button__label">{buttoncancellabel}</span>
            </button>
          {/if}
          {#if !!buttonoklabel}
            <button
              tabindex="0"
              type="button"
              class="mdc-button mdc-dialog__button mdc-button--{buttonokvariant}"
              data-mdc-dialog-action="ok"
              on:click={ok}
            >
              <div class="mdc-button__ripple" />
              <span class="mdc-button__label">{buttonoklabel}</span>
            </button>
          {/if}
        </div>
      </div>
    </div>
    <div class="mdc-dialog__scrim" on:click={dismiss} />
  </div>
</main>

<style lang="scss">
  @use "@material/button/styles";
  @use "@material/dialog";
  @use "@material/icon-button";
  @import "@material/icon-button/mdc-icon-button";
  @import "material-icons/iconfont/material-icons.scss";

  @include dialog.core-styles;
</style>
