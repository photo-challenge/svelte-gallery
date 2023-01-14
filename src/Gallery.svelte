<script>
  import Img from './lib/Img.svelte';
  import layout from './lib/layout';

  export let images = [];
  export let rowHeight = 220;
  export let gutter = 8;
  export let imageComponent = Img;

  let scaledImages = [];
  let width = 0;

  function imgStyle({ scaledWidth, scaledHeight, isLastInRow, isLastRow }) {
    let marginRight = gutter + 'px',
      flex = `0 0 ${scaledWidth}px`,
      marginBottom = isLastRow ? '0' : marginRight;

    if (isLastInRow) {
      marginRight = '0';
      flex = `1 1 ${scaledWidth - 4}px`;
    }

    return `height: ${scaledHeight}px; flex: ${flex}; margin-right: ${marginRight}; margin-bottom: ${marginBottom};`;
  }

  $: scaledImages = layout({
    images,
    containerWidth: width || 1280,
    targetHeight: rowHeight,
    gutter
  });

</script>

<style>
  .gallery-masonry {
    max-width: 100%;
  }

  .gallery-container {
    display: flex;
    flex-wrap: wrap;
  }

  .gallery-image {
    position: relative;
    height: 100%;
  }

  .gallery-image > :global(*) {
    width: 100%;
    height: 100%;
  }

  .gallery-hidden {
    visibility: hidden;
  }

</style>

<div class="gallery-masonry" bind:clientWidth={width}>
  <div class="gallery-container" style="width: {width}px" class:gallery-hidden={!width}>
    {#each scaledImages as { index, ratio, scaledHeight, scaledWidth, isLastInRow, isLastRow, scaledWidthPc, ...image }}
      <div
        class="gallery-image"
        style={imgStyle({ scaledHeight, scaledWidth, isLastInRow, isLastRow })}
      >
        <slot {index} {image}>
          <svelte:component this={imageComponent} {...image} />
        </slot>
      </div>
    {/each}
  </div>
</div>
