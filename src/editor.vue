<template lang="html">
  <div :id="id"></div>
</template>

<script>
import E from 'wangeditor'
import './editor.css'
import { fullscreen } from './plugins/fullscreen'
import { clearStyle } from './plugins/clearStyle'
import { clearFormat } from './plugins/clearFormat'
import { clearAll } from './plugins/clearAll'
import { insertCode } from './plugins/insertCode'
export default {
  name: 'VueWangeditor',
  mixins: [fullscreen, clearStyle, clearFormat, clearAll, insertCode],
  props: {
    options: {
      type: Object,
      default:  _ => { return {} }
    },
    id: String,
    initContent: String,
    disabled: Boolean
  },
  data () {
    return {
      editor: {}
    }
  },
  watch: {
    initContent (val) {
      this.editor.txt.html(val)
      this.$emit('input', val)
      this.$emit('update:text', this.editor.txt.text())
    },
    disabled (val) {
      this.editor.$textElem.attr('contenteditable', !val)
    }
  },
  computed: {
    editorEle () {
      if (document.querySelector(`#${this.id}`)) {
        return document.querySelector(`#${this.id}`)
      }
    },
    toolbar () {
      if (document.querySelector(`#${this.id} .w-e-toolbar`)) {
        return document.querySelector(`#${this.id} .w-e-toolbar`)
      }
    },
    container () {
      if (document.querySelector(`#${this.id} .w-e-text-container`)) {
        return document.querySelector(`#${this.id} .w-e-text-container`)
      }
    },
    textWrap () {
      if (document.querySelector(`#${this.id} .w-e-text`)) {
        return document.querySelector(`#${this.id} .w-e-text`)
      }
    }
  },
  methods: {
    createBtn (btnHtml) {
      let btn = document.createElement('div')
      btn.className = 'w-e-menu'
      btn.style.zIndex = '10001'
      btn.innerHTML = btnHtml
      return btn
    },
    initSize () {
      if (this.options.width) {
        this.container.style.width = this.options.width
        this.toolbar.style.width = this.options.width
      }
      if (this.options.height) {
        this.container.style.height = this.options.height
      }
    }
  },
  mounted () {
    this.editor = new E('#' + this.id)

    // init change event
    this.editor.customConfig.onchange = html => {
      this.$emit('input', html)
      this.$emit('update:text', this.editor.txt.text())
    }

    // init props options
    Object.keys(this.options).map(property => {
      this.editor.customConfig[property] = this.options[property]
    })

    // create the editor
    this.editor.create()
  
    this.initContent && this.editor.txt.html(this.initContent)

    this.initSize()

    // clearStyle
    if (this.options.menus && this.options.menus.includes('clearStyle')) {
      this.initClearStyle()
    }

    // clearFormat
    if (this.options.menus && this.options.menus.includes('clearFormat')) {
      this.initClearFormat()
    }

    // clearAll
    if (this.options.menus && this.options.menus.includes('clearAll')) {
      this.initClearAll()
    }

    // insertCode
    if (this.options.menus && this.options.menus.includes('insertCode')) {
      this.initInsertCode()
    }

    // fullscreen
    if (this.options.menus && this.options.menus.includes('fullscreen')) {
      this.initFullscreen()
    }

    this.editor.$textElem.attr('contenteditable', !this.disabled)
  }
}
</script>
