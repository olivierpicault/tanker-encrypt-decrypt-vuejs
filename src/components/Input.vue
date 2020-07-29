<template>
  <div>
    <h2>Enter your message</h2>
    <input class="form-control" v-model="value" />
    <button class="btn btn-primary" @click="toggle">{{ encrypted ? 'Decrypt' : 'Encrypt' }}</button>
  </div>
</template>

<script>
import { toBase64, fromBase64 } from '@tanker/client-browser';

export default {
  props: [
    'tanker'
  ],
  data () {
    return {
      value: '',
      encrypted: false
    }
  },
  methods: {
    async toggle () {
      if (this.encrypted) {
        const encryptedData = fromBase64(this.value)
        const clearText = await this.tanker.decrypt(encryptedData)
        this.value = clearText
      } else {
        const encryptedData = await this.tanker.encrypt(this.value)
        const encryptedText = toBase64(encryptedData)
        this.value = encryptedText
      }
      this.encrypted = !this.encrypted
    }
  }
}
</script>

<style scoped>
h2 {
  margin-top: 50px;
}
input {
  width: 50%;
  margin: 25px auto;
}
</style>