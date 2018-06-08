<template>
  <section class="ps-container" :is="$props.tagname" @mouseover.once="update" @ps-scroll-y="scrollHanle" @ps-scroll-x="scrollHanle" @ps-scroll-up="scrollHanle" @ps-scroll-down="scrollHanle" @ps-scroll-left="scrollHanle" @ps-scroll-right="scrollHanle" @ps-y-reach-start="scrollHanle" @ps-y-reach-end="scrollHanle" @ps-x-reach-start="scrollHanle" @ps-x-reach-end="scrollHanle">
    <slot></slot>
  </section>
</template>
<style>
/*
     * Container style
     */
    .ps {
        overflow: hidden !important;
        overflow-anchor: none;
        -ms-overflow-style: none;
        touch-action: auto;
        -ms-touch-action: auto;
    }

    /*
     * Scrollbar rail styles
     */
    .ps__rail-x {
        display: none;
        opacity: 0;
        transition: background-color .2s linear, opacity .2s linear;
        -webkit-transition: background-color .2s linear, opacity .2s linear;
        height: 15px;
        /* there must be 'bottom' or 'top' for ps__rail-x */
        bottom: 0px;
        /* please don't change 'position' */
        position: absolute;
    }

    .ps__rail-y {
        display: none;
        opacity: 0;
        transition: background-color .2s linear, opacity .2s linear;
        -webkit-transition: background-color .2s linear, opacity .2s linear;
        width: 15px;
        /* there must be 'right' or 'left' for ps__rail-y */
        right: 0;
        /* please don't change 'position' */
        position: absolute;
    }

    .ps--active-x > .ps__rail-x,
    .ps--active-y > .ps__rail-y {
        display: block;
        background-color: transparent;
    }

    .ps:hover > .ps__rail-x,
    .ps:hover > .ps__rail-y,
    .ps--focus > .ps__rail-x,
    .ps--focus > .ps__rail-y,
    .ps--scrolling-x > .ps__rail-x,
    .ps--scrolling-y > .ps__rail-y {
        opacity: 0.6;
    }

    .ps .ps__rail-x:hover,
    .ps .ps__rail-y:hover,
    .ps .ps__rail-x:focus,
    .ps .ps__rail-y:focus,
    .ps .ps__rail-x.ps--clicking,
    .ps .ps__rail-y.ps--clicking {
        background-color: #eee;
        opacity: 0.9;
    }

    /*
     * Scrollbar thumb styles
     */
    .ps__thumb-x {
        background-color: #aaa;
        border-radius: 6px;
        transition: background-color .2s linear, height .2s ease-in-out;
        -webkit-transition: background-color .2s linear, height .2s ease-in-out;
        height: 6px;
        /* there must be 'bottom' for ps__thumb-x */
        bottom: 2px;
        /* please don't change 'position' */
        position: absolute;
    }

    .ps__thumb-y {
        background-color: #aaa;
        border-radius: 6px;
        transition: background-color .2s linear, width .2s ease-in-out;
        -webkit-transition: background-color .2s linear, width .2s ease-in-out;
        width: 6px;
        /* there must be 'right' for ps__thumb-y */
        right: 2px;
        /* please don't change 'position' */
        position: absolute;
    }

    .ps__rail-x:hover > .ps__thumb-x,
    .ps__rail-x:focus > .ps__thumb-x,
    .ps__rail-x.ps--clicking .ps__thumb-x {
        background-color: #999;
        height: 11px;
    }

    .ps__rail-y:hover > .ps__thumb-y,
    .ps__rail-y:focus > .ps__thumb-y,
    .ps__rail-y.ps--clicking .ps__thumb-y {
        background-color: #999;
        width: 11px;
    }

    /* MS supports */
    @supports (-ms-overflow-style: none) {
        .ps {
            overflow: auto !important;
        }
    }

    @media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {
        .ps {
            overflow: auto !important;
        }
    }

.ps-container {
  position: relative;
}
</style>
<script>
import scrollBar from 'perfect-scrollbar'

export default {
  name: 'vue-perfect-scrollbar',
  props: {
    settings: {
      default: undefined
    },
    swicher: {
      type: Boolean,
      default: true,
    },
    tagname: {
      type: String,
      default: "section"
    }
  },
  methods: {
    scrollHanle(evt) {
      this.$emit(evt.type, evt)
    },

    update() {
      scrollBar.update(this.$el)
    },

    __init() {
      if (this.swicher) {
        if (!this._ps_inited) {
          this._ps_inited = true
          scrollBar.initialize(this.$el, this.settings)
        } else {
          this.update(this.$el)
        }
      }
    },

    __uninit() {
      scrollBar.destroy(this.$el)
      this._ps_inited = false
    },
  },

  watch: {
    swicher(val) {
      if (val && !this._ps_inited) {
        this.__init()
      }
      if (!val && this._ps_inited) {
        this.__uninit()
      }
    },

    $route() {
      this.update()
    },

  },

  mounted() {
    // for support ssr
    if (!this.$isServer) {
      this.__init()
    }
  },

  updated() {
    this.$nextTick(this.update)
  },

  activated() {
    this.__init()
  },

  deactivated() {
    this.__uninit()
  },

  beforeDestroy() {
    this.__uninit()
  },
}
</script>
