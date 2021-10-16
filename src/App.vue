<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.svg">
    <InvoiceCalculator/>
  </div>
</template>

<script>
import InvoiceCalculator from './components/InvoiceCalculator.vue'

export default {
  name: 'App',
  components: {
    InvoiceCalculator
  }
}
</script>

<style src="./../src/assets/scss/main.scss" lang="scss"></style>
