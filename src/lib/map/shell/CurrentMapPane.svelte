<!--
  CurrentMapPane.svelte — modern map for historical/current comparison.

  The pane shares the exact same OpenLayers View as the historical map.
  Pan, zoom and rotation are therefore synchronized by construction, with no
  event relay and no drift between panes.
-->
<script lang="ts">
  import { onDestroy, onMount } from 'svelte';
  import OlMap from 'ol/Map';
  import type BaseLayer from 'ol/layer/Base';
  import { Attribution, ScaleLine } from 'ol/control';
  import { defaults as defaultControls } from 'ol/control/defaults';
  import { createBasemapLayers } from './basemapLayers';

  export let primaryMap: OlMap;
  export let initialBasemap: string = 'g-streets';
  export let map: OlMap | null = null;

  let container: HTMLDivElement;
  let secondaryMap: OlMap | null = null;
  let basemapLayers: Map<string, BaseLayer> = new Map();
  let currentBasemap: 'g-streets' | 'g-satellite' =
    initialBasemap === 'g-satellite' ? 'g-satellite' : 'g-streets';

  function applyBasemap(key: 'g-streets' | 'g-satellite') {
    currentBasemap = key;
    basemapLayers.forEach((layer, k) => layer.setVisible(k === key));
    secondaryMap?.render();
  }

  $: map = secondaryMap;

  onMount(() => {
    basemapLayers = createBasemapLayers();

    secondaryMap = new OlMap({
      target: container,
      layers: Array.from(basemapLayers.values()),
      // Sharing the View is the important part: both panes always look at the
      // exact same ground extent, zoom and rotation.
      view: primaryMap.getView(),
      controls: defaultControls({
        attribution: false,
        rotate: false,
        zoom: false,
      }).extend([new Attribution(), new ScaleLine()]),
    });

    applyBasemap(currentBasemap);

    requestAnimationFrame(() => {
      requestAnimationFrame(() => secondaryMap?.updateSize());
    });
  });

  onDestroy(() => {
    secondaryMap?.setTarget(undefined);
    secondaryMap = null;
    basemapLayers.clear();
  });
</script>

<div class="current-pane">
  <div bind:this={container} class="current-map"></div>

  <div class="pane-card" aria-label="Bản đồ hiện nay">
    <div class="eyebrow"><span class="live-dot"></span> HIỆN NAY / CURRENT</div>
    <strong>Bản đồ nền hiện đại</strong>
    <span class="hint">Đồng bộ vị trí · tỷ lệ · góc quay</span>
  </div>

  <div class="basemap-switch" aria-label="Chọn bản đồ nền hiện nay">
    <button
      type="button"
      class:is-active={currentBasemap === 'g-streets'}
      aria-pressed={currentBasemap === 'g-streets'}
      on:click={() => applyBasemap('g-streets')}>Phố</button
    >
    <button
      type="button"
      class:is-active={currentBasemap === 'g-satellite'}
      aria-pressed={currentBasemap === 'g-satellite'}
      on:click={() => applyBasemap('g-satellite')}>Vệ tinh</button
    >
  </div>

  <div class="crosshair" aria-hidden="true"><span></span><i></i></div>
</div>

<style>
  .current-pane {
    position: relative;
    width: 100%;
    height: 100%;
    overflow: hidden;
    background: var(--sb-bg, #f4f2ec);
  }

  .current-map {
    position: absolute;
    inset: 0;
  }

  .pane-card {
    position: absolute;
    top: 14px;
    left: 14px;
    z-index: 20;
    display: grid;
    gap: 2px;
    max-width: min(310px, calc(100% - 28px));
    padding: 10px 12px;
    border: 1px solid color-mix(in srgb, var(--sb-text, #111) 14%, transparent);
    border-radius: 10px;
    background: color-mix(in srgb, var(--sb-bg, #fff) 92%, transparent);
    color: var(--sb-text, #171717);
    box-shadow: 0 8px 28px rgb(0 0 0 / 0.13);
    backdrop-filter: blur(12px) saturate(1.15);
    pointer-events: none;
  }

  .eyebrow {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 0.62rem;
    font-weight: 800;
    letter-spacing: 0.09em;
  }

  .live-dot {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    background: #2d8a57;
    box-shadow: 0 0 0 3px rgb(45 138 87 / 0.16);
  }

  .pane-card strong {
    font-family: var(--sb-font-display, system-ui);
    font-size: 0.95rem;
    line-height: 1.2;
  }

  .hint {
    color: var(--sb-text-meta, #666);
    font-size: 0.68rem;
  }

  .basemap-switch {
    position: absolute;
    top: 14px;
    right: 14px;
    z-index: 21;
    display: flex;
    gap: 3px;
    padding: 3px;
    border: 1px solid rgb(0 0 0 / 0.12);
    border-radius: 9px;
    background: rgb(255 255 255 / 0.92);
    box-shadow: 0 6px 20px rgb(0 0 0 / 0.12);
    backdrop-filter: blur(10px);
  }

  .basemap-switch button {
    border: 0;
    border-radius: 6px;
    padding: 6px 9px;
    background: transparent;
    color: #4a4a4a;
    font: 700 0.68rem/1 system-ui, sans-serif;
    cursor: pointer;
  }

  .basemap-switch button.is-active {
    background: #1c1d1f;
    color: #fff;
  }

  .crosshair {
    position: absolute;
    left: 50%;
    top: 50%;
    z-index: 15;
    width: 26px;
    height: 26px;
    transform: translate(-50%, -50%);
    pointer-events: none;
    opacity: 0.68;
  }

  .crosshair::before {
    content: '';
    position: absolute;
    inset: 8px;
    border: 1px solid rgb(20 20 20 / 0.7);
    border-radius: 50%;
    box-shadow: 0 0 0 1px rgb(255 255 255 / 0.7);
  }

  .crosshair span,
  .crosshair i {
    position: absolute;
    background: rgb(20 20 20 / 0.65);
    box-shadow: 0 0 0 1px rgb(255 255 255 / 0.5);
  }

  .crosshair span {
    left: 12px;
    top: 0;
    width: 1px;
    height: 26px;
  }

  .crosshair i {
    top: 12px;
    left: 0;
    width: 26px;
    height: 1px;
  }

  @media (max-width: 700px) {
    .pane-card {
      top: 9px;
      left: 9px;
      padding: 8px 10px;
    }

    .pane-card .hint {
      display: none;
    }

    .basemap-switch {
      top: 9px;
      right: 9px;
    }
  }
</style>
