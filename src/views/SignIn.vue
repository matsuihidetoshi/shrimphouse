<template>
  <div class="signin">
    <div v-if="!signedIn">
      <amplify-authenticator v-bind:authConfig="authConfig" />
    </div>
    <div v-if="signedIn">
      <amplify-sign-out />
    </div>
  </div>
</template>
<script lang="ts">
import { AmplifyEventBus } from 'aws-amplify-vue'
import { Auth } from 'aws-amplify'
import { Component, Vue } from 'vue-property-decorator'

@Component
export default class SignIn extends Vue {
  signedIn = false
  authConfig: any = {
    signUpConfig: {
      hideAllDefaults: true,
      defaultCountryCode: '1',
      signUpFields: [
        {
          label: 'Email',
          key: 'username',
          required: true,
          displayOrder: 1,
          type: 'string',
        },
        {
          label: 'Password',
          key: 'password',
          required: true,
          displayOrder: 2,
          type: 'password'
        }
      ]
    }
  }

  async beforeCreate() {
    try {
      await Auth.currentAuthenticatedUser()
      this.signedIn = true
    } catch (err) {
      this.signedIn = false
    }
    AmplifyEventBus.$on('authState', info => {
      if (info === 'signedIn') {
        this.signedIn = true
      } else {
        this.signedIn = false
      }
    })
  }
}
</script>
<style>
.signin {
  margin: 20px;
}
input {
  font-size: 16px !important;
}
@media screen and (max-width: 480px) {
  .signin div {
    min-width: 0;
  }
}
</style>
