<script>
export default {
  name: 'Q-Dashboard',
  props: {
    width: {
      type: Number,
      default: 240
    },
    showSider: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      headerHeight: '100%'
    }
  },
  mounted() {
    setTimeout(() => {
      this.updateHeaderHeight();
    }, 300);
    window.addEventListener('resize', this.updateHeaderHeight);
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.updateHeaderHeight);
  },
  methods: {
    updateHeaderHeight() {
      const contentHeight = document.querySelector('.q-dashboard-content').scrollHeight;
      const viewportHeight = window.innerHeight;
      this.headerHeight = contentHeight > viewportHeight ? contentHeight : 'auto';
      this.headerHeight = this.headerHeight + 100 + 'px';
      console.log('headerHeight', this.headerHeight);
    },
  },
}
</script>
<template lang="html">
<main class="q-dashboard" :style="`--sider-width: ${width}`" :class="`${ showSider ? 'show-sider' : 'hide-sider'}`">
  <aside class="q-dashboard-sider h-full" v-if="showSider"  :style="`height: ${headerHeight};`">
    <slot name="sider"></slot>
  </aside>
  <section class="q-dashboard-header mb-2" style="overflow-y: scroll; max-height: 50px;">
    <slot name="header"></slot>
  </section>
  <!-- <section class="q-dashboard-content" style="overflow-y: scroll;"> -->
    <section class="q-dashboard-content">
    <slot name="content"></slot>
  </section>
  <section class="q-dashboard-footer">
    <slot name="footer"></slot>
  </section>
</main>
</template>
<style lang="scss" scoped>
.q-dashboard {
  $sider-width: calc(var(--sider-width) * 1px);
  display: grid;
  grid-template-rows: 50px auto auto auto;
  padding: 0;
  margin: 0;
  grid-template-areas:
    "sider header"
    "sider content"
    "sider footer";
  .q-dashboard-sider {
    grid-area: sider;
    overflow: hidden;
    // background: var(--color-black-6);

    /* Background Gradient */
    background: var(--Gradient-backgrpund, linear-gradient(175deg, #73F7FF -20.12%, #0EA5E9 51.52%, #037AD0 92.93%));

    /* Add the image as a second background layer */
    background-image:
        url(../../assets/image/sidebar/Vector-130.png), /* Bottom image */
        linear-gradient(175deg, #73F7FF -20.12%, #0EA5E9 51.52%, #037AD0 92.93%); /* Gradient */

    /* Position the image at the bottom */
    background-position: bottom center, center;

    /* Ensure image doesn't repeat */
    background-repeat: no-repeat;

    /* Cover the image (adjust if needed) */
    background-size: auto, cover;
    height: 100%;
    min-height: 100vh;
    width: $sider-width;
    transition: 0.5s width;
  }
  .q-dashboard-header {
    grid-area: header;
    // max-width: calc(100% - $sider-width);
  }
  .q-dashboard-content {
    grid-area: content;
    // margin: 10px;
    // max-width: calc(100% - $sider-width);
  }
  .q-dashboard-footer {
    grid-area: footer;
    // max-width: calc(100% - $sider-width);
  }
}
.show-sider {
    $sider-width: calc(var(--sider-width) * 1px);
    // grid-template-columns: $sider-width calc(100% - $sider-width);
    grid-template-columns: $sider-width calc(100% - $sider-width);
  }
.hide-sider {
    grid-template-columns: 0 auto; /* Hide the sidebar */
  }
</style>
