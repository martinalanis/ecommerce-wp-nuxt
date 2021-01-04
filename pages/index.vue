<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <div class="text-center">
        <logo />
        <vuetify-logo />
      </div>
      <v-card>
        <v-card-title class="headline">
          Welcome to the Vuetify + Nuxt.js template
        </v-card-title>
        <v-card-text>
          <p>Vuetify is a progressive Material Design component framework for Vue.js. It was designed to empower developers to create amazing applications.</p>
          <p>
            For more information on Vuetify, check out the <a
              href="https://vuetifyjs.com"
              target="_blank"
              rel="noopener noreferrer"
            >
              documentation
            </a>.
          </p>
          <p>
            If you have questions, please join the official <a
              href="https://chat.vuetifyjs.com/"
              target="_blank"
              rel="noopener noreferrer"
              title="chat"
            >
              discord
            </a>.
          </p>
          <p>
            Find a bug? Report it on the github <a
              href="https://github.com/vuetifyjs/vuetify/issues"
              target="_blank"
              rel="noopener noreferrer"
              title="contribute"
            >
              issue board
            </a>.
          </p>
          <p>Thank you for developing with Vuetify and I look forward to bringing more exciting features in the future.</p>
          <div class="text-xs-right">
            <em><small>&mdash; John Leider</small></em>
          </div>
          <hr class="my-3">
          <a
            href="https://nuxtjs.org/"
            target="_blank"
            rel="noopener noreferrer"
          >
            Nuxt Documentation
          </a>
          <br>
          <a
            href="https://github.com/nuxt/nuxt.js"
            target="_blank"
            rel="noopener noreferrer"
          >
            Nuxt GitHub
          </a>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="primary"
            nuxt
            to="/inspire"
          >
            Continue
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
    <v-col cols="12">
      Fetched indo
      <v-list-item
        v-for="(item, i) in productos"
        :key="i"
      >
        <v-list-item-content>
          <v-list-item-title>{{ item.name || '--'}}</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
    </v-col>
  </v-row>
</template>

<script>
import OAuth from 'oauth-1.0a'
import CryptoJS from 'crypto-js'
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'

export default {
  components: {
    Logo,
    VuetifyLogo
  },
  data () {
    return {
      productos: [],
      api: null,
      header: '',
      local: {
        url: 'http://localhost/ecommerce',
        ck: 'ck_4bd2099e3a73990a651c3ecb7c53e8996573f6df',
        cs: 'cs_b7ef0ed3fc0c35edaee869019782c1b85af5b4af'
      },
      remote: {
        url: 'https://apiwpc.nodolab.com.mx',
        ck: 'ck_8ef3ca9c96d393fa59f0355568ff592b5a84d58a',
        cs: 'cs_09938092d5012fd4c0fac9454e330ebf4027d089'
      }
    }
  },
  created () {
    const data = this.remote
    const oauth = OAuth({
      consumer: {
        key: data.ck,
        secret: data.cs
      },
      signature_method: 'HMAC-SHA1',
      hash_function: (baseString, key) => {
        return CryptoJS.enc.Base64.stringify(CryptoJS.HmacSHA1(baseString, key))
      }
    })

    console.log('oauth', oauth)

    const requestData = {
      url: `${data.url}/wp-json/wc/v3/products`,
      method: 'GET'
    }
    const auth = oauth.authorize(requestData)
    // console.log(this.auth)
    const str = `oauth_consumer_key="${auth.oauth_consumer_key}", oauth_nonce="${auth.oauth_nonce}", oauth_signature="${auth.oauth_signature}", oauth_signature_method="${auth.oauth_signature_method}", oauth_timestamp="${auth.oauth_timestamp}", oauth_version="${auth.oauth_version}"`
    console.log(str)
    this.header = str
    this.getData()
  },
  methods: {
    async getData () {
      this.productos = await this.$axios.$get(`${this.remote.url}/wp-json/wc/v3/products`, {
        headers: {
          Authorization: `OAuth ${this.header}`
        }
      })
    }
  }
}
</script>
