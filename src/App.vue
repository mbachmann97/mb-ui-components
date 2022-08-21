<template>
  h:<input v-model="wrapperHeight" type="range" min="50" max="800" step="5">
  w:<input v-model="wrapperWidth" type="range" min="50" :max="maxWidth" step="5">
  <div id="components-wrapper" :style="{width: wrapperWidth + 'px', height: wrapperHeight + 'px'}">
    <input type="text">
    <mb-input id="test-input" validation v-model="mbInputValue" :error="formErrors.testInput" label="test" />
    <mb-input id="test-input" validation v-model="mbInputValue" :error="formErrors.testInput" label="test" />
    <mb-input id="test-input" validation lazy v-model="mbInputValue" :error="formErrors.testInput" label="test" />
    <mb-input id="test-input" validation v-model="mbInputValue" :error="formErrors.testInput" label="test" />
    <mb-input id="test-input" validation v-model="mbInputValue" :error="formErrors.testInput" label="test" />

    <mb-input id="test-input" validation lazy v-model="mbEmailValue" :error="formErrors.email" label="Email" />
    {{mbInputValue}}
  </div>
</template>

<script>

import MbInput from '@/components/MbInput'

export default {
  name: 'App',
  components: {
    MbInput
  },
  data() {
    return {
      /*TODO: move to global*/ emailRegex: new RegExp(/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/),
      wrapperHeight: 200,
      wrapperWidth: 500,
      maxWidth: 0,

      // mb-input data
      mbInputValue: '',
      mbEmailValue: '',
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
      formErrors: {
        testInput: [],
        email: []
      }
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
  // checking the rules against inputs
  watch: {
    mbInputValue(newMbInputValue) {
      this.formErrors.testInput = []
      this.inputValidators.forEach(rule => this.formErrors.testInput.push(rule(newMbInputValue)))
    },
    mbEmailValue(newMbEmailValue) {
      this.formErrors.email = []
      this.emailRules.forEach(rule => this.formErrors.email.push(rule(newMbEmailValue)))
    }
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
  background-color: #ccc;
  height: 10rem;
  width: 10rem;
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
}

</style>
