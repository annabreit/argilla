<template>
  <span
    :class="['tooltip__container', showTooltip ? 'active' : null]"
    @click="active()"
  >
    <slot></slot>
    <span
      v-if="showTooltip && tooltip"
      class="tooltip"
      :class="tooltipClass"
      v-text="tooltip"
    />
  </span>
</template>

<script>
export default {
  data: () => {
    return {
      showTooltip: false,
    };
  },
  props: {
    tooltip: {
      type: String,
    },
    tooltipPosition: {
      type: String,
      default: "right",
    },
  },
  computed: {
    tooltipClass() {
      return `--${this.tooltipPosition}`;
    },
  },
  methods: {
    active() {
      this.showTooltip = true;
      setTimeout(() => {
        this.showTooltip = undefined;
      }, 1000);
    },
  },
};
</script>

<style scoped lang="scss">
.tooltip {
  position: absolute;
  top: 100%;
  background: palette(grey, 100);
  display: inline-block;
  border-radius: $border-radius-s;
  color: palette(white);
  @include font-size(12px);
  box-shadow: 0 1px 4px 1px rgba(222, 222, 222, 0.5);
  padding: 0.1em 0.5em;
  white-space: nowrap;
  font-family: $primary-font-family !important;
  &.--left {
    right: 0;
  }
  &.--right {
    left: 0;
  }
  &__container {
    position: relative;
    &.active {
      :deep(svg) {
        .breadcrumbs &,
        .code & {
          fill: $brand-secondary-color;
        }
      }
    }
  }
}
</style>
