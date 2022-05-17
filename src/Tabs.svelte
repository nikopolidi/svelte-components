<svelte:options tag="element-factory-md-tabs" />

<script>
  import { MDCTabBar } from "@material/tab-bar";
  import { MDCTab } from "@material/tab";
  import { MDCTabIndicator } from "@material/tab-indicator";
  import { MDCTabScroller } from "@material/tab-scroller";
  import "material-icons";
  import { onMount } from "svelte";

  export let activeindex = 0;
  export let datasource;

  let element;

  if (!datasource) {
    ({ activeindex, datasource } = applyDefaults());
  }
  if (datasource) {
    if (datasource.constructor === String) {
      try {
        datasource = JSON.parse(datasource);
      } catch (err) {
        console.error("datasource parse error:", err);
        datasource = [];
      }
    }
  }

  function applyDefaults() {
    return {
      datasource: [
        {
          label: "Tab 1",
          icon: "list",
          content: "<h1>Tab 1 content with list icon</h1>",
        },
        {
          label: "Tab 2",
          icon: "person",
          content: "<h1>Tab 2 content with profile icon</h1>",
        },
      ],
      activeindex: 0,
    };
  }
  /* function injectMaterialIconsStylesheet(){
    const _link = document.createElement("link");
    _link.href = "https://fonts.googleapis.com/icon?family=Material+Icons";
    _link.rel = "stylesheet";
    element.insertBefore(_link, element.querySelector(".globalCss"));
  } */

  onMount(() => {
    /* inject materialIcons script */
    // injectMaterialIconsStylesheet()

    /* instantiate mdc components */
    const mdcTabBar = element.querySelector(".mdc-tab-bar");
    if (mdcTabBar) {
      const tabBar = new MDCTabBar(mdcTabBar);
    }
    const mdcTab = element.querySelector(".mdc-tab");
    if (mdcTab) {
      const tab = new MDCTab(mdcTab);
    }
    const mdcTabIndicator = element.querySelector(".mdc-tab-indicator");
    if (mdcTabIndicator) {
      const tabIndicator = new MDCTabIndicator(mdcTabIndicator);
    }
    const mdcTabScroller = element.querySelector(".mdc-tab-scroller");
    if (mdcTabScroller) {
      const tabScroller = new MDCTabScroller(mdcTabScroller);
    }
  });

  function setActiveIndex(index) {
    activeindex = +index;
  }
</script>

<svelte:head>
  <link
    rel="stylesheet"
    href="https://fonts.googleapis.com/icon?family=Material+Icons"
  />
</svelte:head>

<main bind:this={element}>
  <div class="globalCss">
    <div class="mdc-tab-bar" role="tablist">
      <div class="mdc-tab-scroller">
        <div class="mdc-tab-scroller__scroll-area">
          <div class="mdc-tab-scroller__scroll-content">
            {#each datasource as el, index}
              <button
                class="mdc-tab itemCss"
                role="tab"
                class:mdc-tab--active={activeindex == index}
                aria-selected={(activeindex == index).toString()}
                tabindex={index}
                on:click={() => setActiveIndex(index)}
              >
                <span class="mdc-tab__content itemCss">
                  <span class="mdc-tab__icon material-icons" aria-hidden="true"
                    >{el.icon}</span
                  >
                  <span class="mdc-tab__text-label itemCss">{el.label}</span>
                </span>
                <span
                  class="mdc-tab-indicator itemCss"
                  class:mdc-tab-indicator--active={activeindex == index}
                >
                  <span
                    class="mdc-tab-indicator__content mdc-tab-indicator__content--underline"
                  />
                </span>
                <span class="mdc-tab__ripple" />
              </button>
            {/each}
          </div>
        </div>
      </div>
    </div>
    {#if datasource[activeindex]?.content}
      <div class="itemCss">
        {@html datasource[activeindex].content}
      </div>
    {/if}
  </div>
</main>

<style lang="scss">
  @use "@material/tab-bar/mdc-tab-bar";
  @use "@material/tab-scroller/mdc-tab-scroller";
  @use "@material/tab-indicator/mdc-tab-indicator";
  @use "@material/tab/mdc-tab";
  @import "material-icons/iconfont/material-icons.scss";
  // @import "material-icons/iconfont/material-icons.scss";
</style>
