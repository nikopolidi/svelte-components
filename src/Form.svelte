<svelte:options tag="element-factory-md-form-control" />

<script>
  import { onMount } from "svelte";
  import { MDCFormField } from "@material/form-field";
  import { MDCRadio } from "@material/radio";
  import { MDCCheckbox } from "@material/checkbox";
  import { MDCTextField } from "@material/textfield";
  import { MDCSelect } from "@material/select";
  import { MDCMenu } from "@material/menu";
  import { MDCNotchedOutline } from "@material/notched-outline";
  import { createForm, key } from "svelte-forms-lib";
  import { MDCLineRipple } from "@material/line-ripple";
  import { MDCFloatingLabel } from "@material/floating-label";
  import { MDCRipple } from "@material/ripple";
  // import "@material/select";
  // import * as mdc from "material-components-web";
  // import "@material/auto-init";

  export let horizontal = false;
  export let datasource;
  export let submitbtnlabel = "Submit";
  export let submitbtnvariant = "raised";
  export let cancelbtnenabled = true;
  export let cancelbtnlabel = "Cancel";
  export let cancelbtnvariant = "contained";
  export let demo = true;
  // comment next line before adding to EF
  let element;

  let formRoot;
  let initialValues;
  /**
   *  @description: Applies component default props
   * and transforms string props to JS array/object.
   */
  initComponentData();

  function parseJSONArrayProp(val, propName) {
    if (!val) return [];
    if (val.constructor === String) {
      try {
        return JSON.parse(val);
      } catch (err) {
        console.error(`Failed parsing json array property ${propName}:`, err);
        return [];
      }
    } else if (val.constructor === Array) {
      return val;
    } else {
      return [];
    }
  }

  /* 
  defaults applied if some values missing and has defaults 
  */
  function applyDefaults() {
    const defaultProps = {
      datasource: [
        {
          name: "checkbox",
          label: "CheckBox",
          value: "true",
          inputType: "checkbox",
          dataType: "boolean",
        },
        /* 
        // checkbox with multiple values not implemented
        {
          name: "checkbox_multiple",
          label: "CheckBox Multiple",
          value: "yes",
          inputType: "checkbox",
          dataType: "array",
          multi: true,
          options: [{
            label: "No",
            value: "no",
          },
          {
            label: "Yes",
            value: "yes",
          },
          {
            label: "May Be",
            value: "maybe",
          }]
        }, 
        */
        {
          name: "radio",
          label: "Radio",
          value: "2",
          inputType: "radio",
          options: [
            {
              label: "Option 1",
              value: "1",
            },
            {
              label: "Option 2",
              value: "2",
            },
          ],
          dataType: "number",
        },

        {
          name: "boolean_radio",
          label: "Boolean Radio",
          value: false,
          inputType: "radio",
          options: [
            {
              label: "Yes",
              value: true,
            },
            {
              label: "No",
              value: false,
            },
          ],
          dataType: "boolean",
        },
        {
          name: "select",
          label: "Single Select",
          value: "false",
          multi: false,
          inputType: "select",
          options: [
            {
              label: "Yes",
              value: "true",
            },
            {
              label: "No",
              value: "false",
            },
          ],
          dataType: "string",
        },
        {
          name: "select2",
          label: "Second Select",
          value: "2",
          multi: false,
          inputType: "select",
          options: [
            {
              label: "Option 1",
              value: "1",
            },
            {
              label: "Option 2",
              value: "2",
            },
          ],
          dataType: "string",
        },
        {
          name: "textInput",
          label: "textInput",
          value: "",
          inputType: "text",
          dataType: "string",
          require: true,
        },
      ],
    };
    return defaultProps;
  }

  function transformProps() {
    return {
      datasource: parseJSONArrayProp(datasource, "datasource"),
    };
  }
  function handleCancel() {
    console.log("handleCancel", handleReset);
    handleReset();
  }
  function initComponentData(e) {
    ({ datasource } = applyDefaults());
    ({ datasource } = transformProps());

    initialValues = datasource.reduce((m, el) => {
      // console.log("el", el);
      switch (el.dataType) {
        case "boolean":
          m[el.name] = el.value ? !!new Boolean(JSON.parse(el.value)) : false;
          break;
        case "number":
          m[el.name] = +el.value || 0;
        default:
          break;
      }
      if (el.dataType === "boolean") {
      } else {
        m[el.name] = el.value;
      }
      return m;
    }, {});
  }

  const {
    form,
    handleChange,
    handleSubmit,
    isValid,
    isSubmitting,
    errors,
    handleReset,
  } = createForm({
    initialValues,
    onSubmit: (values) => {
      alert(JSON.stringify(values));
    },
    validate: (values) => {
      console.log("values", values);
      const requiredKeys = Object.keys(values).filter(
        (key) =>
          [true, "true"].includes(datasource[key].required) && !values[key]
      );
      const validationResult = Object.assign(
        {},
        ...Array.from(requiredKeys, ([k, v]) => ({
          [k]: `${datasource[key].label} required.`,
        }))
      );
      console.log("validationResult", validationResult);
      return validationResult;
    },
  });

  onMount(() => {
    instantiateMaterialComponents();
  });

  function handleSelectChange(name, value) {
    $form[name] = value;
  }

  function instantiateMaterialComponents() {
    const mdcSelectElements = formRoot.querySelectorAll(".mdc-select");
    formRoot
      .querySelectorAll(".mdc-form-field-select")
      .forEach((mdcFormFieldSelect, index) => {
        const selectFormField = new MDCFormField(mdcFormFieldSelect);
        const mdcSelect = mdcSelectElements[index];

        if (mdcSelect) {
          const select = new MDCSelect(mdcSelect);
          // selectFormField.input = select;

          select.listen("MDCSelect:change", (event) => {
            /**
             * @note event.target here is null
             * use onclick on option and handle with handleSelectChange
             */
          });
        }
      });
    /*  instantiate button */
    const mdcButton = document.querySelector(".mdc-button");
    if (mdcButton) {
      const buttonRipple = new MDCRipple(mdcButton);
    }
    /* instantiate radio */
    const mdcRadio = formRoot.querySelector(".mdc-radio");
    if (mdcRadio) {
      const radio = new MDCRadio(mdcRadio);
      const mdcRadioFormField = formRoot.querySelector(".mdc-form-field-radio");
      const radioFormField = new MDCFormField(mdcRadioFormField);
      radioFormField.input = radio;
    }
    /*  */

    /* instantiate menu */
    const mdcMenu = formRoot.querySelector(".mdc-menu");
    if (mdcMenu) {
      const menu = new MDCMenu(mdcMenu);

      menu.listen("MDCMenu:open", () => {
        // console.log("on open");
      });
    }
    /*  */

    /* instantiate floating-label */
    const mdcFormTextField = formRoot.querySelector(".mdc-form-field-text");
    if (mdcFormTextField) {
      const formFieldText = new MDCFormField(mdcFormTextField);
      const mdcTextField = formRoot.querySelector(".mdc-text-field");
      if (mdcTextField) {
        const textInput = new MDCTextField(mdcTextField);
        formFieldText.input = textInput;
      }
    }
    const mdcFloatingLabel = element.querySelector(".mdc-floating-label");
    if (mdcFloatingLabel) {
      const floatingLabel = new MDCFloatingLabel(mdcFloatingLabel);
    }
    /* instantiate textinput */
    const mdcNotchedOutline = document.querySelector(".mdc-notched-outline");
    if (mdcNotchedOutline) {
      const notchedOutline = new MDCNotchedOutline(mdcNotchedOutline);
    }
    const mdcLineRipple = document.querySelector(".mdc-line-ripple");
    if (mdcLineRipple) {
      const lineRipple = new MDCLineRipple(mdcLineRipple);
    }
  }

  function openMenu() {
    menuOpen = true;
  }
</script>

<svelte:head>
  <link
    href="https://unpkg.com/material-components-web@latest/dist/material-components-web.min.css"
    rel="stylesheet"
  />
</svelte:head>

<main bind:this={element}>
  <div class="globalCss">
    <form
      on:submit={handleSubmit}
      class="form-wrapper"
      class:form-horizontal={horizontal}
      bind:this={formRoot}
    >
      {#each datasource as el, elIndex}
        {#if el.inputType === "checkbox"}
          <label for="{el.name}-{elIndex}" class="mdc-typography--subtitle2">
            {el.label}
          </label>
          <div class="mdc-form-field itemCss">
            {#if el.multi}
              <!-- miltiple checkbox not implemented -->
            {:else}
              <div class="mdc-checkbox">
                <input
                  type="checkbox"
                  class="mdc-checkbox__native-control"
                  id="checkbox-1"
                  bind:checked={$form[el.name]}
                  on:change={handleChange}
                />
                <div class="mdc-checkbox__background">
                  <svg class="mdc-checkbox__checkmark" viewBox="0 0 24 24">
                    <path
                      class="mdc-checkbox__checkmark-path"
                      fill="none"
                      d="M1.73,12.91 8.1,19.28 22.79,4.59"
                    />
                  </svg>
                  <div class="mdc-checkbox__mixedmark" />
                </div>
                <div class="mdc-checkbox__ripple" />
              </div>
              <label for="checkbox-1">Checkbox 1</label>
            {/if}
          </div>
        {:else if el.inputType === "text"}
          <div class="mdc-form-field itemCss mdc-form-field-text">
            <!-- <label
              class="mdc-text-field mdc-text-field--filled"
              data-mdc-auto-init="MDCTextField"
            >
              <span class="mdc-text-field__ripple" />
              <input
                class="mdc-text-field__input"
                type="text"
                aria-labelledby="label"
                name={el.name}
                bind:value={$form[el.name]}
              />
              <span id="label" class="mdc-floating-label">Input Label</span>
              <span class="mdc-line-ripple" />
            </label> -->

            <label class="mdc-text-field mdc-text-field--filled">
              <span class="mdc-text-field__ripple" />
              <!-- 
                for span below:
                if mdc-floating-label--float-above class added it will work as expected
                snippet:
                class:mdc-floating-label--float-above={!!$form[el.name]}
              -->
              <span
                class="mdc-floating-label"
                class:mdc-floating-label--float-above={!!$form[el.name]}
                id={el.name}>{el.label}</span
              >
              <input
                class="mdc-text-field__input"
                type="text"
                aria-labelledby={el.name}
                required={["true", true].includes(el.required) && "required"}
                aria-required={el.required}
                aria-controls="{el.name}-helper"
                aria-describedby="{el.name}-helper"
                name={el.name}
                bind:value={$form[el.name]}
              />
              <span class="mdc-line-ripple" />
            </label>
            <div class="mdc-text-field-helper-line">
              {#if el.required}
                <div
                  class="mdc-text-field-helper-text"
                  id="{el.name}-helper"
                  aria-hidden="true"
                >
                  {#if $errors[el.name]}
                    {$errors[el.name]}
                  {:else}
                    * required
                  {/if}
                </div>
              {/if}
            </div>
          </div>
        {:else if el.inputType === "radio"}
          <label for="{el.name}-{elIndex}" class="mdc-typography--subtitle2">
            {el.label}
          </label>
          {#each el.options as option, index}
            <div class="mdc-form-field itemCss mdc-form-field-radio">
              <div class="mdc-radio">
                <input
                  id="{el.name}-{index}"
                  class="mdc-radio__native-control"
                  type="radio"
                  value={option.value}
                  name={el.name}
                  on:change={handleChange}
                  checked={$form[el.name] == option.value}
                />
                <div class="mdc-radio__background">
                  <div class="mdc-radio__outer-circle" />
                  <div class="mdc-radio__inner-circle" />
                </div>
                <div class="mdc-radio__ripple" />
              </div>
              <label for="radio-{index}">{option.label}</label>
              <!-- content here -->
            </div>
          {/each}
        {:else if el.inputType === "select"}
          <div class="mdc-form-field itemCss mdc-form-field-select">
            <div class="mdc-select mdc-select--filled" id={el.name}>
              <input
                type="hidden"
                name={el.name}
                on:change={handleChange}
                bind:value={$form[el.name]}
              />
              <div
                class="mdc-select__anchor"
                role="button"
                aria-haspopup="listbox"
                aria-expanded="true"
                aria-required={el.required}
                aria-labelledby="{el.name}-label {el.name}-selected-text"
              >
                <span class="mdc-select__ripple" />
                <span
                  id="{el.name}-label"
                  class="mdc-floating-label"
                  class:mdc-floating-label--float-above={!!$form[el.name]}
                  >{el.label}</span
                >
                <span class="mdc-select__selected-text-container">
                  <span
                    id="{el.name}-selected-text"
                    class="mdc-select__selected-text"
                  />
                </span>
                <span class="mdc-select__dropdown-icon">
                  <svg
                    class="mdc-select__dropdown-icon-graphic"
                    viewBox="7 10 10 5"
                    focusable="false"
                  >
                    <polygon
                      class="mdc-select__dropdown-icon-inactive"
                      stroke="none"
                      fill-rule="evenodd"
                      points="7 10 12 15 17 10"
                    />
                    <polygon
                      class="mdc-select__dropdown-icon-active"
                      stroke="none"
                      fill-rule="evenodd"
                      points="7 15 12 10 17 15"
                    />
                  </svg>
                </span>
                <span class="mdc-line-ripple" />
              </div>
              <div
                class="mdc-select__menu mdc-menu mdc-menu-surface mdc-menu-surface--fullwidth"
              >
                <ul
                  class="mdc-list"
                  role="listbox"
                  aria-label="Food picker listbox"
                >
                  {#each el.options as option}
                    <li
                      class="mdc-list-item"
                      aria-selected="false"
                      data-value={option.value}
                      on:click={() => handleSelectChange(el.name, option.value)}
                      role="option"
                      class:mdc-list-item--selected={$form[el.name] ==
                        option.value}
                      class:mdc-list-item--disabled={false}
                    >
                      <span class="mdc-list-item__ripple" />
                      <span class="mdc-list-item__text"> {option.label} </span>
                    </li>
                  {/each}
                </ul>
              </div>
            </div>
          </div>
        {:else}
          <!-- UNKOWN -->
        {/if}
      {/each}
      <div class="mdc-form-field">
        {#if !!cancelbtnenabled}
          <button
            class="mdc-button mdc-button--{cancelbtnvariant}"
            type="button"
            on:click={handleCancel}
          >
            <span class="mdc-button__ripple" />
            <span class="mdc-button__label">{cancelbtnlabel}</span>
          </button>
        {/if}
        <button
          class="mdc-button mdc-button--{submitbtnvariant}"
          type="submit"
          disabled={$isValid == false || $isSubmitting}
        >
          <span class="mdc-button__ripple" />
          <span class="mdc-button__label">{submitbtnlabel}</span>
        </button>
      </div>
    </form>
    {#if demo}
      <h4>Demo Output</h4>
      {JSON.stringify($form, null, 2)}
      <h4>Errors</h4>
      {JSON.stringify($errors, null, 2)}
    {/if}
  </div>
</main>

<style lang="scss">
  // @use "material-components-web/material-components-web";
  @use "@material/typography/mdc-typography";
  @use "@material/button/styles" as button;
  @use "@material/icon-button";
  @use "@material/chips";
  @use "@material/checkbox";
  @use "@material/radio/styles" as radio;
  @use "@material/form-field";
  // select
  @use "@material/list/mdc-list";
  @use "@material/menu-surface/mdc-menu-surface";
  @use "@material/menu/mdc-menu";
  @use "@material/select/styles";
  // textfield
  @use "@material/floating-label/mdc-floating-label";
  @use "@material/line-ripple/mdc-line-ripple";
  @use "@material/notched-outline/mdc-notched-outline";
  @use "@material/textfield";
  // floating-label
  // @use "@material/floating-label/mdc-floating-label";
  // icon-button
  @import "@material/icon-button/mdc-icon-button";
  @import "material-icons/iconfont/material-icons.scss";

  @include checkbox.core-styles;
  @include form-field.core-styles;
  @include textfield.core-styles;
  .mdc-form-field {
    margin: 8px 0 8px 0;
  }
  select {
    align-self: flex-start;
    height: 42px;
    min-width: 200px;
    text-align: left;
    background-color: whitesmoke;
    border-bottom-right-radius: 0;
    border-bottom-left-radius: 0;
    display: inline-flex;
    align-items: baseline;
    padding: 8px 16px;
    position: relative;
    box-sizing: border-box;
    overflow: hidden;
    will-change: opacity, transform, color;
  }
  .form-wrapper {
    display: flex;
    flex-direction: column;
  }
  .form-horizontal {
    flex-direction: row;
  }
  .column {
    flex-direction: column;
  }
</style>
