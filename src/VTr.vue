<template>
  <tr
    :class="[rowClass]"
    :style="style"
    @click="handleOnClick"
  >
    <slot></slot>
  </tr>
</template>

<script>
import { isEqual } from 'lodash-es'

export default {
  name: 'v-tr',
  props: {
    onClick: {
      type: Function,
      default: null
    },
    row: {
      required: true
    }
  },
  inject: ['store'],
  data () {
    return {
      state: this.store._data
    }
  },
  mounted () {
    if (this.onClick || !this.state.customSelection) {
      this.$el.style.cursor = 'pointer'
    }
  },
  beforeDestroy () {
    if (this.onClick || !this.state.customSelection) {
      this.$el.removeEventListener('click', this.handleOnClick)
    }
  },
  computed: {
    isSelected () {
      return this.state.selectedRows.find(row => isEqual(row, this.row))
    },
    rowClass: function () {
      return this.isSelected ? this.state.selectedClass : ''
    },
    style () {
      return {
        ...(this.onClick || !this.state.customSelection ? { cursor: 'pointer' } : {})
      }
    }
  },
  methods: {
    handleOnClick (event) {
      if (this.onClick) {
        this.onClick()
      } else {
        this.handleRowSelected(event)
      }
    },

    handleRowSelected (event) {
      if (this.state.customSelection) return

      let source = event.target || event.srcElement
      if (source.tagName.toLowerCase() === 'td') {
        if (this.isSelected) {
          this.store.deselectRow(this.row)
        } else {
          this.store.selectRow(this.row)
        }
      }
    }
  }
}
</script>
