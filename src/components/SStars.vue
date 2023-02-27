<template>
  <div
    class="stars"
    :style="`--rating: ${rating};`"
    :aria-label="`Rating of this product is ${rating} out of 5.`"
  />
</template>

<script>
export default {
  props: {
    rating: {
      type: Number,
      default: 0
    }
  }
}
</script>

<style lang="scss" scoped>
.stars {
  --percent: calc(var(--rating) / 5 * 100%);
  display: inline-block;
  font: normal normal bold 20px/27px Open Sans;
  line-height: 1;
  position: relative;
  width: 100%;

  &::before {
    position: absolute;
    bottom: 0;
    right: 0;

    content: '★★★★★';
    letter-spacing: 2px;
    color: var(--c-blue);
    background: linear-gradient(90deg, var(--c-blue) var(--percent), var(--c-white) var(--percent));
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
  }
  &::after {
    position: absolute;
    bottom: 0;
    right: 0;

    content: '☆☆☆☆☆';
    letter-spacing: 2px;
    color: var(--c-blue);
  }
}
</style>
