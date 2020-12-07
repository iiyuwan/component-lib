<template>
  <div>
    <div ref="container" :style="{ width, height }"></div>
    <div class="jsoneditor-btns">
      <button
        class="json-save-btn"
        type="button"
        @click="onSave()"
        :disabled="error"
      >
        保存
      </button>
    </div>
  </div>
</template>

<script>
import JSONEditor from 'jsoneditor'
import 'jsoneditor/dist/jsoneditor.css'
export default {
  name: 'jsoneditor',
  props: {
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '500px'
    },
    mode: {
      type: String,
      default: 'tree'
    },
    modes: {
      type: Array,
      default: function () {
        return ['tree', 'code', 'form', 'text', 'view']
      }
    },
    json: {
      type: Object,
      default: () => {}
    },
    schema: {
      type: Object
      // default: () => {}
    },
    expandedOnStart: {
      type: Boolean,
      default: true
    }
  },
  data () {
    return {
      editor: null,
      error: true,
      expandedModes: ['tree', 'view', 'form']
    }
  },
  mounted () {
    this.initData()
  },
  watch: {
    json: {
      deep: true,
      handler (val) {
        this.editor.set(val)
      }
    },
    schema: {
      deep: true,
      handler (val) {
        this.editor.options.schema = val
      }
    }
  },
  methods: {
    initData () {
      const self = this
      const options = {
        mode: this.mode,
        schema: this.schema,
        modes: this.modes, // allowed modes
        onModeChange () {
          self.expandAll()
        },
        onValidationError: function (errors) {
          if (errors.length) {
            self.error = true
          } else {
            self.error = false
          }
        }
      }
      this.editor = new JSONEditor(this.$refs.container, options)
      this.editor.set(this.json)
    },
    expandAll () {
      if (
        this.expandedOnStart &&
        this.expandedModes.includes(this.editor.getMode())
      ) {
        this.editor.expandAll()
      }
    },
    onSave () {
      // 返回最新的json值
      if (!this.error) {
        const val = this.editor.get()
        this.$emit('json-save', val)
      }
    }
  }
}
</script>

<style lang="less" >
.jsoneditor-btns {
  text-align: center;
  margin-top: 10px;
}
.json-save-btn {
  background-color: #20a0ff;
  border: none;
  color: #fff;
  padding: 5px 10px;
  width: 50px;
  border-radius: 5px;
  cursor: pointer;
}
.json-save-btn:focus {
  outline: none;
}
.json-save-btn[disabled] {
  background-color: #1d8ce0;
  cursor: not-allowed;
}
.jsoneditor-menu{
  background: #57C5F7;
}
</style>
