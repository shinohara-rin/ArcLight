<template>
  <div v-if="!error" class="get-audio-info">
    <slot v-bind:card="card" />
  </div>
</template>

<script>
import { localCache } from '@/util/cache'

export default {
  components: {},
  props: {
    txid: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      loading: true,
      error: false,
      loadingCard: {
        txid: this.txid,
        title: 'Loading...',
        price: '0000',
        authorUsername: 'Artist loading...'
      },
      card: {}
    }
  },
  watch: {
    txid (val) {
      if (val) {
        this.card = this.loadingCard
        this.getInfo()
      }
    }
  },
  created () {
    this.card = this.loadingCard
  },
  mounted () {
    this.$nextTick(() => {
      this.loadingCard.title = this.$t('loading')
      this.loadingCard.authorUsername = this.$t('artistLoading')
    })
    this.getInfo()
  },
  methods: {
    async getInfo () {
      try {
        const cachedData = await localCache.getInfoByTxid(this.txid)
        if (cachedData) {
          this.card = cachedData
        } else {
          this.error = true
        }
      } catch (e) {
        console.error(this.txid, e)
        this.error = true
      }
      this.loading = false
    }
  }
}
</script>

<style lang="less" scoped>
.get-audio-info {
  display: inline-block;
}
</style>
