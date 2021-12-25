<template>
  <div>
    <OrganismsHeader
      :nickname="nickname"
    />
    <main>
      <Nuxt />
    </main>
  </div>
</template>

<script>
export default {
  name: 'LayoutsDefault',
  computed: {
    nickname () {
      const token = this.$auth.strategy.token.get()
      if (token) {
        const payload = this.parseJwt(token)
        return payload.nickname
      } else {
        return ''
      }
    }
  },
  methods: {
    parseJwt (token) {
      const base64Url = token.split('.')[1]
      const base64 = base64Url.replace(/-/g, '+').replace(/_/g, '/')
      const jsonPayload = decodeURIComponent(atob(base64).split('').map((c) => {
        return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
      }).join(''))

      return JSON.parse(jsonPayload)
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
</style>
