<style module>
  .ctn {
    width: 240px;
    height: 90px;
  }
</style>

<template>
  <div class="ve-popover" :class="$style.ctn" :style="style" v-show="showPopup">
    <div class="ve-pop-arrow"></div>
    <div class="ve-pop-header">{{lang.title}}</div>
    <div class="ve-pop-body">
      <div class="ve-input-box">
        <input type="text" class="ve-input" v-model="val">
        <button type="button" class="ve-btn" @click="linkHandler">{{lang.ok}}</button>
      </div>
    </div>
  </div>
</template>

<script>
  import veMixin from '../mixins'
  import { getLang } from '../config/lang.js'

  export default {
    mixins: [veMixin],
    data () {
      return {
        val: '',
        lang: getLang('link')
      }
    },
    computed: {
      showPopup: function () {
        return this.$store.state.toolbar.link.showPopup
      },
      callee: function () {
        return this.$store.state.callee
      },
      range: function () {
        return this.$store.state.range
      },
    },
    watch: {
      'callee': function (val) {
        val.name === 'unLink' && this.unLinkHandler()
      },
      'showPopup': function (val) {
        val && this.setVal()
      }
    },
    methods: {
      checkValid () {
        let href = this.val
        href.match(/^https?:\/\//igm) === null && (href = 'http://' + href)
        return href
      },
      setVal () {
        this.val = ''
        if (this.range) {
          let container = this.range.commonAncestorContainer
          container.nodeType === 3 && (container = container.parentNode)
          container && container.nodeName ==='A' &&  (this.val = container.href)
        }
      },
      linkHandler () {
        this.unLinkHandler()
        let href = this.checkValid()
        this.$store.dispatch('execCommand', { name: 'createlink', value: href })
        this.$store.dispatch('updatePopupDisplay')
      },
      unLinkHandler () {
        this.$store.dispatch('execCommand', { name: 'unlink', value: null })
      }
    }
  }
</script>