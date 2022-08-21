<template>
  <div class="input-wrap">
    <div class="label-placeholder"></div>
    <div class="input-and-label">
      <input 
        type="text"
        :class="{'error-border': error.err && validation}"
        :value="modelValue"
        placeholder=" "
        @input="(e) => {if(!lazy) $emit('update:modelValue', e.target.value)}"
        @change="(e) => {if(lazy) $emit('update:modelValue', e.target.value)}"
      > <!--input-->
      <label>{{ label }}</label>
    </div>
    <div v-if="validation" :class="{message: true, 'error-bgcolor': triggeredErrors.length > 0}">
      <span v-if="triggeredErrors.length > 0">
        {{ errorString }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "MbInput",
  props: {
    modelValue: {
      type: String,
      required: true
    },
    label: {
      type: String,
      required: false
    },
    validation: {
      type: Boolean,
      required: false
    },
    lazy: {
      type: Boolean,
      required: false
    },
    error: {
      type: Array,
      required: false
    }
  },
  computed: {
    triggeredErrors() {
      return this.error.filter(err => err.err)
    },
    errorString() {
      let out = this.triggeredErrors[0]?.message
      this.triggeredErrors.forEach(err => {
        if (err.message !== out) out += ' • ' + err.message
      })
      return out
    }
  }
}
</script>

<style scoped>
  .input-and-label {
    position: relative;
    height: 2.3rem;
  }

  .label-placeholder {
    height: 1rem;
  }

  input {
    height: 100%;
    width: 100%;
    padding: 0 .5rem;
    box-sizing: border-box;
    border: none;
  }

  input:active, input:focus {
    outline: none;
  }

  input + label {
    opacity: .8;
    user-select: none;
    pointer-events: none;
    position: absolute;
    left: .5rem;
    top: 50%;
    transform: translateY(-50%);
    transition: all .2s ease;
  }

  input:not(:placeholder-shown) + label,
  input:focus + label
  {
    opacity: 1;
    top: 0rem;
    transform: translateY(-100%);
    font-size: .8rem;
  }

  .message {
    height: 1rem;
    line-height: 1rem;
    text-align: center;
    padding: .1rem .5rem;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    transition: all .15s ease;
    text-align:left;
    font-size: .8rem;
    color: #ff0033
  }

  .message span::selection {
   background-color: #ff0033;
   color: white;
  }

  .error-bgcolor {
    background-color: #ff003328;
  }

  .error-border {
    border: 1px solid #ff0033;
  }
</style>