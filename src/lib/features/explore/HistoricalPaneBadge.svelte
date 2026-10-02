<!-- Compact label for the historical half of the Then / Now workspace. -->
<script lang="ts">
  export let title: string = 'Bản đồ lịch sử';
  export let year: number | null = null;
  export let opacity: number = 1;

  $: opacityPct = Math.round(Math.max(0, Math.min(1, opacity)) * 100);
</script>

<div class="historical-card" aria-label="Bản đồ xưa">
  <div class="eyebrow"><span class="history-dot"></span> BẢN ĐỒ XƯA / HISTORICAL</div>
  <strong>{title}</strong>
  <div class="meta">
    <span>{year ?? 'Chưa rõ năm'}</span>
    <span class="sep">·</span>
    <span>Độ phủ {opacityPct}%</span>
  </div>
</div>

<div class="crosshair" aria-hidden="true"><span></span><i></i></div>

<style>
  .historical-card {
    position: absolute;
    top: 14px;
    left: 14px;
    z-index: 40;
    display: grid;
    gap: 2px;
    max-width: min(360px, calc(100% - 28px));
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

  .history-dot {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    background: #a05d2a;
    box-shadow: 0 0 0 3px rgb(160 93 42 / 0.16);
  }

  strong {
    overflow: hidden;
    font-family: var(--sb-font-display, system-ui);
    font-size: 0.95rem;
    line-height: 1.2;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .meta {
    display: flex;
    gap: 5px;
    color: var(--sb-text-meta, #666);
    font-size: 0.68rem;
  }

  .sep {
    opacity: 0.45;
  }

  .crosshair {
    position: absolute;
    left: 50%;
    top: 50%;
    z-index: 35;
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
    .historical-card {
      top: 9px;
      left: 9px;
      padding: 8px 10px;
      max-width: calc(100% - 18px);
    }

    .historical-card strong {
      max-width: 210px;
    }
  }
</style>
