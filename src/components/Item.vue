<template>
  <div class="item" :class="[figure]" :style="style">
    <div class="item__description">
      {{ weight + 'kg' }}
    </div>
  </div>
</template>

<script>
import * as items from '../settings';

export default {
  name: 'Item',
  props: {
    figure: {
      type: String,
      default: items.SQUARE,
    },
    weight: {
      type: Number,
      default: 5,
    },
  },
  computed: {
    scale() {
      return (50 + 5 * this.weight) / 100;
    },
    style() {
      return {
        transform: `scale(${this.scale})`,
        width: `${items.ITEM_SIZE}px`,
        height: `${items.ITEM_SIZE}px`,
      };
    },
  },
};
</script>

<style scoped lang="scss">
  $item-width: 80px;
.item {
  position: relative;
  text-align: center;
  color: white;

  &:before {
    content: "";
    position: absolute;
    display: block;
    background: #232427;
    width: 100%;
    height: 100%;
    z-index: -1;
  }

  &.square:before {}

  &.circle:before {
    border-radius: 50%;
  }

  &.triangle:before {
    background: none;
    width: 0;
    height: 0;
    border-style: solid;
    border-width: $item-width 0 0 $item-width;
    border-color: transparent transparent transparent black;
  }

  .item__description {
    position: absolute;
    width: 100%;
    font-size: 19px;
    bottom: 1px;
  }
}
</style>
