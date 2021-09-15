<template>
  <dvi>
    <v-row justify="center" align="center">
      <v-col cols="12">
        <h1>ECOMMERCE</h1>
      </v-col>
    </v-row>
    <v-row>
      <v-col
        v-for="(item, i) in productos"
        :key="i"
        cols="3"
      >
        <v-card
          width="100%"
        >
          <v-img
            height="250"
            :src="item.images[0].src"
          />
          <v-card-title class="pb-0">{{ item.name || '--' }}</v-card-title>
          <v-card-text>
            <v-row>
              <v-col cols="12">
                <span class="grey--text subtitle-1">
                  $ {{ item.price }}
                </span>
              </v-col>
              <v-col cols="12" v-html="item.short_description">
              </v-col>
            </v-row>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="cyan darken-3"
              small
            >
              <v-icon left>mdi-cart-outline</v-icon>
              Comprar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </dvi>
</template>

<script>
import OAuth from 'oauth-1.0a'
import CryptoJS from 'crypto-js'

export default {
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
