<template>
  <div id="app">
    <nav class="border-b bg-white">
      <div class="container mx-auto flex justify-between">
        <router-link to="/" class="font-bold no-underline text-grey-darkest  py-4">Coordinates Calculator</router-link>
      </div>
    </nav>
    <router-view class="container mx-auto"/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
  @import './assets/app.css';
  body {
    font-family: 'Roboto', sans-serif;
  }
</style>
