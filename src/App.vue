<template>
  <div id="app">
    <div v-if="ready">
      <h1>tanker-encrypt-decrypt tutorial</h1>
      <Input :tanker="tanker" />
    </div>
    <div v-else>
      <p>Loading</p>
    </div>
  </div>
</template>

<script>
import FakeAuthentication from '@tanker/fake-authentication'
import Tanker from '@tanker/client-browser'

import Input from './components/Input.vue'

const appId = 'YOUR APP ID'

const user = {
  email: 'bruce@wayne-entreprises.com',
  passphrase: 'iamthebatman'
}

export default {
  components: {
    Input
  },
  data () {
    return {
      tanker: null,
      fakeAuth: null,
      ready: false,
    }
  },
  created () {
    this.tanker = new Tanker({ appId })
    this.fakeAuth = new FakeAuthentication({ appId })
  },
  async mounted () {
    const { email, passphrase } = user
    const { identity } = await this.fakeAuth.getIdentity(email)
    const status = await this.tanker.start(identity)
    switch (status) {
      case Tanker.statuses.IDENTITY_REGISTRATION_NEEDED:
        await this.tanker.registerIdentity({ passphrase })
        break
      case Tanker.statuses.IDENTITY_VERIFICATION_NEEDED:
        await this.tanker.verifyIdentity({ passphrase })
        break
      case Tanker.statuses.READY:
        break
      default:
        throw new Error(`Unhandled status: ${status}`)
    }
    this.ready = true
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
</style>
