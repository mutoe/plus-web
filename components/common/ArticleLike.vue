<template>
  <section class="c-article-like">
    <div class="meta-item">
      <Button
        size="large"
        class="meta-button"
        :class="{active: collected}"
        @click="onCollect"
      >
        <svg class="icon"><use xlink:href="#icon-collect" /></svg>
        {{ collected ? '取消收藏' : '收藏' }}
      </Button>
      <Button
        size="large"
        class="meta-button"
        :class="{active: liked}"
        @click="onLike"
      >
        <svg class="icon"><use xlink:href="#icon-like" /></svg>
        {{ likeCount }} 人喜欢
      </Button>
    </div>

    <div class="meta-item">
      <SocialShare v-if="Object.keys(share).length" v-bind="share" />
    </div>
  </section>
</template>

<script>
import SocialShare from '@/components/common/SocialShare.vue'

export default {
  name: 'ArticleLike',
  components: {
    SocialShare,
  },
  props: {
    likeCount: { type: Number, default: 0 },
    collectCount: { type: Number, default: 0 },
    liked: { type: Boolean, default: false },
    collected: { type: Boolean, default: false },

    /**
     * @property {Object} share
     * @property {string} share.title 分享标题
     * @property {string} share.description 分享内容
     * @property {string} share.image 图片链接
     */
    share: { type: Object, default: () => {} },
  },
  data () {
    return {
      collectLock: false,
      likeLock: false,
    }
  },
  methods: {
    async onCollect () {
      await this.checkAuth()
      if (this.collectLock) return
      this.collectLock = true
      this.$emit('collect', () => {
        this.collectLock = false
      })
    },
    async onLike () {
      await this.checkAuth()
      if (this.likeLock) return
      this.likeLock = true
      this.$emit('like', () => {
        this.likeLock = false
      })
    },
  },
}
</script>

<style lang="less" scoped>
.c-article-like {
  display: flex;
  justify-content: space-between;

  .meta-item {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .meta-button {
    margin-right: 12px;

    &.active {
      color: @primary-color;
    }

    .icon {
      margin-right: 4px;
    }
  }

  .share-icon {
    margin-left: 8px;
  }
}
</style>
