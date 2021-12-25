<template>
  <header class="header">
    <div class="header__content">
      <h1>
        Nuxt.jsとRailsとAuth0と
      </h1>
      <div>
        <span
          v-if="nickname"
          class="header__nickname"
        >ようこそ{{ nickname }}さん</span>
        <button
          v-if="loggedIn"
          class="button"
          @click="logout"
        >
          ログアウト
        </button>
        <button
          v-else
          class="button"
          @click="login"
        >
          ログイン
        </button>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  name: 'OrganismsHeader',
  props: {
    nickname: {
      type: String,
      required: false,
      default: ''
    }
  },
  computed: {
    username () {
      const token = this.$auth.strategy.token.get()
      if (token) {
        const payload = this.parseJwt(token)
        return payload.nickname
      } else {
        return ''
      }
    },
    loggedIn () {
      return !!this.$auth.strategy.token.get()
    }
  },
  methods: {
    login () {
      this.$auth.loginWith('auth0')
    },
    logout () {
      this.$auth.logout()
    },
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

<style scoped>
.header {
  height: 60px;
  background-color: #eee;
}

.header__content {
  height: 60px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 0 auto;
  width: calc(100% - 16px);
  max-width: 1024px;
}

.header__nickname {
  font-size: 1.6rem;
}

.button {
  font-size: 1.5rem;
  padding: 0 8px;
}
</style>
