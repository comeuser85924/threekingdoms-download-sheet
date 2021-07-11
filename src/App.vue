<template>
  <div>
    <img class="banner" src="https://cdn-os.sialiagames.com.tw/s3g-tw-gw/1.0.0/prod/views/static/kv.e8ad92ee.jpg"/>
    <HelloWorld/>
    <popup/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import popup from './components/popup.vue'

export default {
  name: 'App',
  components: {
    HelloWorld,popup
  },
}
</script>

<style>

.banner{
  width: 100%;
  object-fit: cover;
  height: 500px;
}
</style>
