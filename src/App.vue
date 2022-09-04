<template>
  h:<input v-model="wrapperHeight" type="range" min="50" max="800" step="5">
  w:<input v-model="wrapperWidth" type="range" min="50" :max="maxWidth" step="5">
  <div id="components-wrapper" :style="{width: wrapperWidth + 'px', height: wrapperHeight + 'px'}">
    Standard HTML <input type="text">
    <mb-input id="test-input" validation radius=".3" prepend="pencil" v-model="mbInputValue" :error="formErrors.testInput" label="test" />
    <mb-input id="test-input" validation radius=".5" v-model="mbInputValue" :error="formErrors.testInput" label="test" />
    <mb-input id="test-input" validation lazy v-model="mbInputValue" :error="formErrors.testInput" label="test" />
    <mb-input id="test-input" validation v-model="mbInputValue" :error="formErrors.testInput" label="test" />
    <mb-input id="test-input" validation v-model="mbInputValue" :error="formErrors.testInput" label="test" />

    <mb-input id="test-input" validation lazy prepend="at" v-model="mbEmailValue" :error="formErrors.email" label="Email" />

    <mb-select
      id="test-input"
      :options="testObjects"
      :representation="option => { return `${option.lastName}, ${option.firstName}` }"
      validation
      prepend="cancel"
      lazy
      v-model="mbSelectValue"
      :error="formErrors.select" 
      label="Select Test"
      no-match-text="no matching person"
      nogap
    />

    <mb-input id="test-input" validation v-model="mbInputValue" :error="formErrors.testInput" label="test" />
    
    {{mbInputValue}}
    <div>outside {{ mbSelectValue }}</div>
  </div>
</template>

<script>

import MbInput from '@/components/MbInput'
import MbSelect from '@/components/MbSelect'

export default {
  name: 'App',
  components: {
    MbInput,
    MbSelect
  },
  data() {
    return {
      /*TODO: move to global*/ emailRegex: new RegExp(/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/),
      wrapperHeight: 540,
      wrapperWidth: 500,
      maxWidth: 0,

      // mb-input data
      mbInputValue: '',
      mbEmailValue: '',
      mbSelectValue: {},

      inputValidators: [
        (value) =>  { return value.length < 1 ?
                    {err:true, message:"This field is required!!!"} :
                    {err:false} },
        (value) =>  { return value.length < 4 ?
                    {err:true, message:"Text too short!"} :
                    {err:false} },
        (value) =>  { return !value.includes('a') ?
                    {err:true, message:"a is mandatory"} :
                    {err:false} },
        (value) =>  { return !value.includes('b') ?
                    {err:true, message:"b is mandatory and you know it"} :
                    {err:false} }
      ],
      emailRules: [
        (value) =>  { return !this.emailRegex.test(value) ?
                    {err:true, message:"Not a valid email"} :
                    {err:false} }
      ],
      selectRules: [ // TODO: write check for wrong selection
        (value) =>  { return value.length < 1 ?
                    {err:true, message:"This field is required!!!"} :
                    {err:false} },
      ],
      formErrors: {
        testInput: [],
        email: [],
        select: []
      },

      testObjects: [
        {firstName: 'Markus', lastName: 'Bachmann', budget: 9000},
        {firstName: 'Peter', lastName: 'Ikk', budget: 1020},
        {firstName: 'Lisa', lastName: 'Szech', budget: 129},
        {firstName: 'Alina', lastName: 'Van Bergen', budget: 87822}
      ]
    }
  },
  created() {
    this.maxWidth = window.innerWidth
    
    window.addEventListener('resize', () => {
      if (this.wrapperWidth > window.innerWidth) {
        this.wrapperWidth = window.innerWidth
      }

      this.maxWidth = window.innerWidth
    })
  },
  methods: {
    validateTest() {
      this.formErrors.testInput = []
      this.inputValidators.forEach(rule => this.formErrors.testInput.push(rule(this.mbInputValue)))
    },
    validateEmail() {
      this.formErrors.email = []
      this.emailRules.forEach(rule => this.formErrors.email.push(rule(this.mbEmailValue)))
    }
  },
  watch: {
    mbInputValue() { this.validateTest() },
    mbEmailValue() { this.validateEmail() }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#components-wrapper {
  background-color: rgb(244, 244, 244);
  height: 10rem;
  width: 10rem;
  padding: 1rem;
  border-radius: 1rem;
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
}

</style>
