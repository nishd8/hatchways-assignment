<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
   watch: {
      // eslint-disable-next-line no-unused-vars
      '$route' (to, from) {
        document.title = to.meta.title || 'Your Website'
      }
    },
}
</script>
