<script>
  import { getContext, createEventDispatcher } from "svelte";
  import { tweened } from "svelte/motion";
  import { cubicOut } from "svelte/easing";
  import mq from "../stores/mq.js";
  import Icon from "../components/helpers/Icon.svelte";

  export let d;
  export let r = 4;

  const dispatch = createEventDispatcher();
  const { xGet, yGet, custom } = getContext("LayerCake");
  const { title, spotifyName, topRank, i } = d;

  $: w = r / $custom.aspectRatio;
  $: h = w;

  $: left = d.x;
  $: top = d.y;
  $: dur = $mq.reducedMotion ? 0 : 1000;
  $: style = `--dur: ${dur}ms; transform: translate3d(${left}px, ${top}px, 0);`;
  $: vol = $custom.preview === d.preview ? "x" : "2";

  const onClick = () => {
    if (d.preview) dispatch("preview", d.preview);
  };
</script>

<div
  class="node-{i}"
  class:band="{d.name === d.band}"
  class:active="{d.band === $custom.activeBand}"
  style="{style}"
  on:click="{onClick}"
>
  <p class="scatter-node-text"><span>#{topRank}</span> {title}</p>
  {#if d.preview}<span class="icon"><Icon name="volume-{vol}" /></span>{/if}
</div>

<style>
  div {
    --dur: 1000ms;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 0;
    will-change: transform;
    border: 1px solid var(--gray-light);
    transition: transform var(--dur) ease-in-out, opacity var(--dur) ease-in-out;
    white-space: nowrap;
    box-shadow: 0 0 3px 0 var(--gray);
    backface-visibility: hidden;
  }

  p {
    display: block;
    position: relative;
    background-color: var(--off-white);
    color: var(--gray-light);
    transition: all var(--dur) ease-in-out;
    user-select: none;
  }

  .active {
    opacity: 1;
    z-index: 1;
    border: 1px solid var(--off-black);
    box-shadow: 0 0 6px 0 var(--off-black);
    cursor: pointer;
  }

  .active:hover {
    z-index: 2;
  }

  .active p {
    color: var(--off-black);
  }

  .node-0.active p {
    background-color: var(--node-0);
    color: var(--white);
  }

  .node-1.active p {
    background-color: var(--node-1);
  }

  .node-2.active p {
    background-color: var(--node-2);
  }

  .node-3.active p {
    background-color: var(--node-3);
  }

  .node-4.active p {
    background-color: var(--node-4);
  }

  .node-5.active p {
    background-color: var(--node-5);
    color: var(--white);
  }

  .icon {
    display: block;
    position: absolute;
    top: 0;
    left: 50%;
    opacity: 0;
    transform: translate(-50%, -50%) scale(1);
    transform-origin: 50% 50%;
    transition: all 150ms ease-out;
    width: 1em;
    z-index: -1;
  }

  .active:hover .icon {
    opacity: 1;
    transform: translate(-50%, -125%) scale(3);
  }
</style>
