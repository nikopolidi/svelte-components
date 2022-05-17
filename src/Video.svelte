<svelte:options tag="element-factory-md-video" />

<script>
  import "material-icons";
  import { onMount } from "svelte";
  import { MDCRipple } from "@material/ripple";

  export let poster = "";
  export let src = "";
  export let height = "320";
  export let width = "480";
  export let controlsposition = $$props["controls-position"] || "bottom";
  export let showControls = true;
  export let autoplay = false;
  export let controls = false;
  export let iconcolor = "white";
  export let timercolor = "white";

  const CONTROLS_TRANSITION_TIMEOUT_MS = 1500;

  if (!src) {
    ({ poster, src } = applyDefaultProps());
  }
  function applyDefaultProps() {
    return {
      poster: "https://res.cloudinary.com/demo/video/upload/ski_jump.jpg",
      src: "https://res.cloudinary.com/demo/video/upload/ski_jump.mp4",
      height: 300,
      controlsposition: "bottom",
    };
  }
  let element;
  // These values are bound to properties of the video
  let time = 0;
  let duration;
  let paused = true;
  let mouseReleased = true;
  let showControlsTimeout;

  // Used to track time of last mouse down event
  let lastMouseDown;

  function onMouseRelease() {}
  // $: console.log('mouseReleased',mouseReleased)
  function handleMove(e) {
    // Make the controls visible, but fade out after
    // CONTROLS_TRANSITION_TIMEOUT_MS = 1500 milliseconds of inactivity
    clearTimeout(showControlsTimeout);
    showControlsTimeout = setTimeout(() => {
      if (!paused) showControls = false;
    }, CONTROLS_TRANSITION_TIMEOUT_MS);
    showControls = true;

    if (!duration) return; // video not loaded yet
    if (e.type !== "touchmove" && !(e.buttons & 1)) return; // mouse not down
    const clientX = e.type === "touchmove" ? e.touches[0].clientX : e.clientX;
    const { left, right } = this.getBoundingClientRect();
    time = (duration * (clientX - left)) / (right - left);
  }

  // we can't rely on the built-in click event, because it fires
  // after a drag — we have to listen for clicks ourselves
  function handleMousedown(e) {
    mouseReleased = false;
    lastMouseDown = new Date();
  }

  function handleMouseup(e) {
    mouseReleased = true;
    const [target] = element.getElementsByTagName("video");
    console.log("target", target);
    if (new Date() - lastMouseDown < 300) {
      if (paused) {
        target.play();
      } else {
        target.pause();
      }
    } else {
      /*  */
    }
  }

  function format(seconds) {
    if (isNaN(seconds)) return "...";

    const minutes = Math.floor(seconds / 60);
    seconds = Math.floor(seconds % 60);
    if (seconds < 10) seconds = "0" + seconds;

    return `${minutes}:${seconds}`;
  }
  onMount(() => {
    const btn = element.querySelector(".mdc-icon-button");
    if (btn) {
      const iconButtonRipple = new MDCRipple(btn);
      iconButtonRipple.unbounded = true;
    }
  });

  function onLoadedMetadata(event) {
    // console.log("onLoadedMetadata", event);
  }
</script>

<main bind:this={element}>
  {#if src}
    <div class="container globalCss">
      <video
        {poster}
        {src}
        {autoplay}
        {controls}
        {height}
        {width}
        on:loadedmetadata={onLoadedMetadata}
        bind:currentTime={time}
        bind:duration
        bind:paused
      >
        <track kind="captions" />
      </video>
      <div
        style="opacity: {duration && showControls ? 1 : 0}"
        class="controls controls-{controlsposition} itemCss"
        on:mousemove={handleMove}
        on:touchmove|preventDefault={handleMove}
        on:mousedown|preventDefault={handleMousedown}
        on:mouseup|preventDefault={handleMouseup}
      >
        <span class="control-button-container itemCss">
          <span 
            class="material-icons md-48 control-button"
            style='color: {iconcolor}'
          >
            {!paused ? "pause_circle" : "play_circle_filled"}</span
          >
        </span>
        <progress
          class="itemCss"
          value={time / duration || 0}
          on:touchmove|stopPropagation={handleMove}
          on:mousedown|stopPropagation={handleMousedown}
          on:mouseup|stopPropagation={handleMouseup}
        />
        <div class="info mdc-typography itemCss">
          <span class="time mdc-typography--body1" style='color: {timercolor};'>{format(time)}</span>
          <span class="time mdc-typography--body1" style='color: {timercolor};'>{format(duration)}</span>
        </div>
      </div>
    </div>
  {/if}
</main>

<style lang="scss">
  @use "@material/icon-button/styles";
  @use "@material/typography/mdc-typography";
  @import "material-icons/iconfont/material-icons.scss";
  $controlsHeight: 48px;
  .container {
    height: fit-content;
    width: fit-content;
    // position: relative;
  }
  .floating-button-container {
    display: flex;
    align-items: center;
    align-content: center;
  }
  .material-icons.md-18 {
    font-size: 18px;
  }
  .material-icons.md-24 {
    font-size: 24px;
  }
  .material-icons.md-36 {
    font-size: 36px;
  }
  .material-icons.md-48 {
    font-size: 48px;
  }
  .icon {
    font-size: 24px;
    color: white;
  }
  div {
    position: relative;
  }
  .control-button-container {
    cursor: pointer;
    position: absolute;
    display: flex;
    align-items: center;
    justify-content: center;
    flex: 1;
    /* right: 50%; */
    height: inherit;
    width: inherit;
  }
  .controls {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: flex-end;
    justify-content: flex-end;
    flex-direction: column;
    transition: opacity 500ms;
  }
  .controls-wrapper {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
  }
  .controls-top {
    position: absolute;
    top: 0;
    width: 100%;
  }
  .controls-bottom {
    position: absolute;
    bottom: 0;
    width: 100%;
  }
  .info {
    display: flex;
    width: 100%;
    justify-content: space-between;
  }

  span {
    padding: 0.2em 0.5em;
    color: white;
    text-shadow: 0 0 8px black;
    font-size: 1.4em;
    opacity: 0.7;
  }

  .time {
    // width: 3em;
  }

  .time:last-child {
    text-align: right;
  }

  progress {
    display: block;
    width: 100%;
    height: 10px;
    -webkit-appearance: none;
    appearance: none;
  }

  progress::-webkit-progress-bar {
    background-color: rgba(0, 0, 0, 0.2);
  }

  progress::-webkit-progress-value {
    background-color: rgba(255, 255, 255, 0.6);
  }

  video {
    width: 100%;
  }
</style>
