<template>
  <div class="select-wrap" v-click-outside="hideOptions">
    <mb-input
      id="test-input"
      v-model="inputValue"
      :label="label"
      :validation="validation"
      :error="error"
      :radius="radius"
      :prepend="prepend"
      @keydown.shift.tab.exact="hideOptions"
      @focus="showOptions = true"
    />
    <!--<Transition>-->
    <div v-if="showOptions && !exactMatch" class="options" ref="options">
      <button
        @keydown.tab.exact="onOptionTab"
        @keydown.shift.tab="test"
        v-for="(option, index) in filteredOptions" :key="index"
        @click="updateModel(option)"
        >
          {{ representation(option) || option }}
        </button>
      <div v-if="filteredOptions.length === 0" id="no-match">There are no matching options</div>
    </div>
    <!--</Transition>-->
    {{ modelValue }}
  </div>
</template>

<script>
import MbInput from '@/components/MbInput'

export default {
  name: 'MbSelect',
  components: {
    MbInput
  },
  props: {
    // input props
    label: {
      type: String,
      required: false
    },
    validation: {
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
    },

    // select props
    modelValue: {
      type: Object,
      required: true
    },
    options: {
      type: Array,
      required: true
    },
    representation: {
      type: Function,
      required: false
    },
    lazy: { // (showing options)
      type: Boolean,
      required: false
    }
  },
  data() {
    return {
      inputValue: '',
      showOptions: false,
      exactMatch: false
    }
  },
  created() {
    this.modelToInput()
  },
  methods: {
    hideOptions() {
      this.showOptions = false
      this.modelToInput()
    },
    onOptionTab(e) {
      for (let i = 0; i < this.$refs.options?.children.length; i++) {
        if (e.target === this.$refs.options?.children[i]) {
          if (i === this.$refs.options?.children.length - 1) {
            this.hideOptions()
            this.modelToInput()
          }
        }
      }
    },
    updateModel(option) {
      this.$emit('update:modelValue', option)
      this.inputValue = this.representation(option)
    },
    modelToInput() {
      if (Object.keys(this.modelValue).length !== 0) {
        this.inputValue = this.representation(this.modelValue)
      }
    }
  },
  computed: {
    filteredOptions() {
      return this.options.filter(opt => this.representation(opt).toLowerCase().includes(this.inputValue.toLowerCase()))
    }
  },
  watch: {
    inputValue(newVal) {
      if (this.filteredOptions[0] && newVal.toLowerCase() === this.representation(this.filteredOptions[0]).toLowerCase()) {
        this.exactMatch = true
        this.updateModel(this.filteredOptions[0])
      } else {
        this.showOptions = true
        this.exactMatch = false
      }
    }
  },
  directives: {
    clickOutside: {
      mounted: (el, binding) => {
        el.__ClickOutsideHandler__ = event => {
          if (!(el === event.target || el.contains(event.target))) {
            binding.value(event)
          }
        }
        document.addEventListener('click', el.__ClickOutsideHandler__)
      },
      unbind: (el) => {
        document.removeEventListener('click', el.__ClickOutsideHandler__)
      }
    }
  }
}
</script>

<style scoped>
  .select-wrap {
    position: relative;
  }

  .options {
    position: absolute;
    padding: .5rem;
    box-sizing: border-box;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    background-color: #1515157b;
    border-radius: 1rem;
    backdrop-filter: blur(.2rem);
    z-index: 99;
  }

  .options button {
    margin: .2rem 0;
    background-color: transparent;
    border: none;
    color: #F8F0E3;
    cursor: pointer;
    user-select: none;
  }

  .options button:hover, .options button:focus {
    border: none;
    outline: none;
    color: white;
    font-weight: bold;
  }

  .options button:first-child {
    margin-top: 0;
  }

  .options button:last-child {
    margin-bottom: 0;
  }

  #no-match {
    color: #F8F0E3;
    margin-left: .4rem;
    font-size: .8rem;
  }

  .v-enter-active,
  .v-leave-active {
    transition: opacity .2s ease;
  }

  .v-enter-from,
  .v-leave-to {
    opacity: 0;
  }
</style>