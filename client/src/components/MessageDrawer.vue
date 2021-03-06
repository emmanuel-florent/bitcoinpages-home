<template>
<aside class="mdc-temporary-drawer mdc-typography" :class="classes">
  <nav ref="drawer" class="mdc-temporary-drawer__drawer">

    <div class="mdc-temporary-drawer__toolbar-spacer" v-if="$slots['toolbar-spacer'] || toolbarSpacer">
      <slot name="toolbar-spacer"></slot>
    </div>

    <header class="mdc-temporary-drawer__header" v-if="$slots.header">
        <slot name="header">
          <p>&nbsp;</p>
          <p>&nbsp;</p>
          <p>&nbsp;</p>          
        </slot>
    </header>
       <nav class="mdc-temporary-drawer__content mdc-list-group">
          <p>&nbsp;</p>
          <p>&nbsp;</p> 
          <div id="icon-with-text-demo" class="mdc-list">
            <a class="mdc-list-item mdc-temporary-drawer--selected" href="#">
              <i class="material-icons mdc-list-item__start-detail" aria-hidden="true">inbox</i>All
            </a>
           </div>
       </nav>
    <slot></slot>
  </nav>
</aside>

</template>

<script lang="babel">

import { MDCTemporaryDrawerFoundation } from '@material/drawer'
import * as utils from '@material/drawer/util'
import { EventBus } from '../event-bus.js'
import DrawerItem from '@/components/DrawerItem'

export default {
  props: ['toolbarSpacer'],
  data () {
    return {
      classes: {},
      changeHandlers: [],
      foundation: null,
      selectedCategory: { id: 0 },
      previousCategory: null,
      treeData: []
    }
  },
  components: {
    'drawer-item': DrawerItem
  },
  created: function () {
    EventBus.$on('toggle-drawer', this.toggle)
  },
  mounted () {
    const {FOCUSABLE_ELEMENTS, OPACITY_VAR_NAME} = MDCTemporaryDrawerFoundation.strings
    let vm = this
    this.foundation = new MDCTemporaryDrawerFoundation({
      addClass (className) {
        vm.$set(vm.classes, className, true)
      },
      removeClass (className) {
        vm.$delete(vm.classes, className)
      },
      hasClass (className) {
        return Boolean(vm.classes[className]) || (vm.$el && vm.$el.classList.contains(className))
      },
      hasNecessaryDom () {
        return Boolean(vm.$refs.drawer)
      },
      registerInteractionHandler (evt, handler) {
        vm.$el.addEventListener(evt, handler)
      },
      deregisterInteractionHandler (evt, handler) {
        vm.$el.removeEventListener(evt, handler)
      },
      registerDrawerInteractionHandler (evt, handler) {
        vm.$refs.drawer.addEventListener(evt, handler)
      },
      deregisterDrawerInteractionHandler (evt, handler) {
        vm.$refs.drawer.removeEventListener(evt, handler)
      },
      registerTransitionEndHandler (handler) {
        vm.$refs.drawer.addEventListener('transitionend', handler)
      },
      deregisterTransitionEndHandler (handler) {
        vm.$refs.drawer.removeEventListener('transitionend', handler)
      },
      registerDocumentKeydownHandler (handler) {
        document.addEventListener('keydown', handler)
      },
      deregisterDocumentKeydownHandler (handler) {
        document.removeEventListener('keydown', handler)
      },
      getDrawerWidth () {
        return vm.$refs.drawer.clientWidth
      },
      setTranslateX (value) {
        vm.$refs.drawer.style.setProperty(
          utils.getTransformPropertyName(),
          value === null ? null : `translateX(${value}px)`
        )
      },
      updateCssVariable (value) {
        vm.$el.style.setProperty(OPACITY_VAR_NAME, value)
      },
      getFocusableElements () {
        return vm.$refs.drawer.querySelectorAll(FOCUSABLE_ELEMENTS)
      },
      saveElementTabState (el) {
        utils.saveElementTabState(el)
      },
      restoreElementTabState (el) {
        utils.restoreElementTabState(el)
      },
      makeElementUntabbable (el) {
        el.setAttribute('tabindex', -1)
      },
      isRtl () {
        /* global getComputedStyle */
        return getComputedStyle(vm.$el).getPropertyValue('direction') === 'rtl'
      }
    })
    this.foundation.init()
  },
  beforeDestroy () {
    this.foundation.destroy()
  },
  methods: {
    toggle () {
      if (this.foundation.isOpen()) {
        this.foundation.close()
      } else {
        this.foundation.open()
      }
    }
  }
}
</script>

<style>
.mdc-temporary-drawer .mdc-list-item {
  color: white;
}
</style>

<style lang="scss">
  @import '@material/drawer/mdc-drawer';
</style>