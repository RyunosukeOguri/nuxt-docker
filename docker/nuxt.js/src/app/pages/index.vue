<template>
  <section class="container">
    <div>
      <logo />
      <h1 class="title">NUXT × Docker</h1>
      <h2 class="subtitle">
        Nuxt.js project - Node.js v11.1
      </h2>
      <div class="links">
        <a-button
          type="primary"
          @click="href('https://vue.ant.design/components/button/')"
        >
          Ant Design Vue Document
        </a-button>
      </div>
    </div>
  </section>
</template>

<script>
import Logo from '~/components/Logo.vue'

export default {
  components: {
    Logo
  },
  methods: {
    href(url) {
      // location.href = url;
      window.open(url)
    }
  }
}
</script>

<style scoped>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
