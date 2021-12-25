<template>
  <div class="wrap">
    <form
      action=""
      onSubmit="return false"
      class="form"
    >
      <div class="form__item">
        <label
          for="title"
          class="form__label"
        >
          タイトル
        </label>
        <input
          id="title"
          v-model="title"
          name="title"
          type="text"
          required
          class="form__input"
        >
      </div>
      <div class="form__item">
        <label
          for="memo"
          class="form__label"
        >
          メモ
        </label>
        <input
          id="memo"
          v-model="memo"
          name="memo"
          type="text"
          class="form__input"
        >
      </div>
      <div class="form__item form__item__submit">
        <button
          type="submit"
          class="form__submit"
          :disabled="!loggedIn"
          @click="postData"
        >
          送信する
        </button>
      </div>
    </form>
    <table class="table">
      <tr class="table__tr">
        <th class="table__th">
          タイトル
        </th>
        <th class="table__th">
          メモ
        </th>
        <th class="table__th">
          作成者
        </th>
      </tr>
      <tr
        v-for="item in memos"
        :key="item.id"
        class="table__tr"
      >
        <td class="table__td">
          {{ item.title }}
        </td>
        <td class="table__td">
          {{ item.memo }}
        </td>
        <td class="table__td">
          {{ item.created_by }}
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data () {
    return {
      title: '',
      memo: '',
      memos: [],
      url: '/api/v1/memos'
    }
  },
  computed: {
    loggedIn () {
      return !!this.$auth.strategy.token.get()
    }
  },
  created () {
    if (this.loggedIn) {
      this.getData()
    }
  },
  methods: {
    async getData () {
      this.memos = await this.$axios.get(this.url)
        .then(response => response.data)
    },
    postData () {
      if (!this.loggedIn) {
        return
      }
      if (this.title.trim() === '') {
        return
      }
      const params = {
        title: this.title,
        memo: this.memo
      }
      this.$axios.post(this.url, params)
        .then(() => {
          this.title = ''
          this.memo = ''
          this.getData()
        })
    }
  }
}
</script>

<style scoped>
.wrap {
  width: calc(100% - 16px);
  max-width: 1024px;
  margin: 0 auto;
}

.form {
  margin: 40px auto;
  width: 80%;
}

.form__item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.form__label {
  font-size: 1.2rem;
}

.form__input {
  width: 80%;
  padding: 8px;
  font-size: 1.4rem;
}

.form__item__submit {
  justify-content: flex-end;
}
.form__submit {
  font-size: 1.2rem;
  padding: 8px;
}

.table {
  width: 80%;
  margin: 0 auto;
  border: 1px solid #ddd;
}

.table__th {
  background-color: #ccc;
  font-size: 1.3rem;
}

.table__tr:nth-child(odd) {
  background-color: #eee;
}

.table__td {
  font-size: 1.2rem;
}
</style>
