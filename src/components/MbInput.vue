<template>
  <div class="input-wrap">
    <div class="label-placeholder"></div>
    <div 
      class="input-and-label"
      :style="{'border-radius': radius + 'rem'}"
    >
      <mdicon class="icon" v-if="prepend" :name="prepend" />
      <input 
        type="text"
        :class="{'error-border': error.err && validation}"
        :style="{'border-radius': radius + 'rem'}"
        :value="modelValue"
        placeholder=" "
        @focus="$emit('focus')"
        @blur="$emit('blur')"
        @input="e => {if(!lazy) $emit('update:modelValue', e.target.value)}"
        @change="e => {if(lazy) $emit('update:modelValue', e.target.value)}"
      > <!--input-->
      <label
        :style="{left: prepend ? 2+'rem' : .5+'rem'}"
      >
        {{ label }}
      </label>
    </div>
    <div
      v-if="validation"
      :class="{message: true, 'error-bgcolor': triggeredErrors.length > 0}"
      :style="{width: `calc(100% - ${radius}rem)`}"
    > <!--error-message-->
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
    },
    radius: {
      type: String,
      required: false
    },
    prepend: {
      type: String,
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
  .input-wrap {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .input-and-label {
    position: relative;
    display: flex;
    align-items: center;
    height: 2.3rem;
    width: 100%;
    background-color: white;
    border: 1px solid #ccc;
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
    left: .5rem !important;
    transform: translateY(-100%);
    font-size: .8rem;
  }

  .message {
    height: 1rem;
    width: 100%;
    box-sizing: border-box;
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

  .icon {
    margin-left: .3rem;
    color: #aaa;
  }
</style>