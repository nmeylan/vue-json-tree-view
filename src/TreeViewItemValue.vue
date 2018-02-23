<template>
  <div>
    <span class="tree-view-item-key">{{keyString}}</span>
    <input v-if="modifiable" class="tree-view-item-value" :class="getValueType(data)" v-model="valueString" @keyup.enter="onUpdateData" @blur="onUpdateData">
    <span v-else class="tree-view-item-value" :class="getValueType(data)">{{ valueFormed }}</span>
    <span v-show="error">{{ error }}</span>
  </div>
</template>


<script>
  export default {
    name: 'tree-view-item',
    props: ['data', 'modifiable', 'key-string'],
    data: function () {
      return {
        valueString: this.data && this.data.toString(),
        error: false,
      }
    },
    computed: {
      valueFormed: function () {
        return this.getValue(this.data)
      }
    },
    methods: {
      onUpdateData: function () {
        try {
          let v = this.typedValue(this.valueString)
          this.error = false
          this.$emit('change-data', [], v)
        }
        catch (err) {
          this.error = err
        }
      },
      typedValue: function (v) {
        if (v === '') { throw 'empty' }

        let dataType = this.getValueType(this.data, '')

        switch (dataType) {
          case 'number':
            let n
            if (isNaN(n = new Number(v))) {
              throw 'only number'
            }
            return n
          case 'boolean':
            if (v.toLowerCase() === 'true') { return true }
            if (v.toLowerCase() === 'false') { return false }
            throw 'true or false'
          case 'string':
          default:
            return v
        }
      },
      getValue: function (value) {
        if (this.isNumber(value)) {
          return value
        }
        if (this.isNull(value)) {
          return 'null'
        }
        if (this.isString(value)) {
          return '"' + value + '"'
        }
        return value
      },
      getValueType: function (value, prefix = 'tree-view-item-value-') {
        if (this.isNumber(value)) {
          return prefix + 'number'
        }
        if (this.isBoolean(value)) {
          return prefix + 'boolean'
        }
        if (this.isNull(value)) {
          return prefix + 'null'
        }
        if (this.isString(value)) {
          return prefix + 'string'
        }
        if (this.isFunction(value)) {
          return prefix + 'function'
        }
        return prefix + 'unknown'
      },
      isNumber (value) {
        return typeof value === 'number'
      },
      isNull (value) {
        return value === null
      },
      isString (value) {
        return typeof value === 'string'
      },
      isBoolean (value) {
        return value === true || value === false
      },
      isFunction (functionToCheck) {
        return functionToCheck && {}.toString.call(functionToCheck) === '[object Function]'
      }
    }
  }
</script>
