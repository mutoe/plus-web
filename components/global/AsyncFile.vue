<template>
  <img
    v-if="type === 'image'"
    :key="`${src}`"
    v-lazyload="src"
    class="c-async-file"
    :class="type"
  >
  <div
    v-else-if="type === 'image-wrap'"
    :key="`${src}`"
    v-lazyload:background="src"
    class="c-async-file"
    :class="type"
  >
    <span v-if="isGif" class="badge gif">GIF</span>
  </div>
  <video
    v-else-if="type === 'video'"
    class="c-async-file"
    :src="getFileUrl(file.video_id)"
    :poster="getFileUrl(file.cover_id)"
    :style="videoStyle"
    controls
  />
</template>

<script>
import { getFileUrl } from '@/utils/file'

export default {
  name: 'AsyncFile',
  props: {
    type: { type: String, default: 'image', validator: val => ['image', 'image-wrap', 'video'].includes(val) },
    url: { type: String, default: '' },
    file: { type: Object, default: () => ({}) },
    maxWidth: { type: Number, default: null },
    maxHeight: { type: Number, default: null },
    quility: { type: Number, default: null },
    blur: { type: Number, default: null },
  },
  data () {
    return {
      query: '',
    }
  },
  computed: {
    fileSystem () {
      return this.file.url ? 'storage' : 'file'
    },
    src () {
      if (this.url) return this.url
      const file = this.fileSystem === 'file' ? (this.file.file || this.file.id) : this.file.url
      return getFileUrl(file, {
        type: this.fileSystem,
        w: this.maxWidth,
        h: this.maxHeight,
        blur: this.blur,
        quility: this.quility,
        query: this.query || this.file._t,
      })
    },
    isGif () {
      if (!['image', 'image-wrap'].includes(this.type)) return false
      return (this.file.mime || '').match(/image\/gif/)
    },
    videoStyle () {
      if (this.type !== 'video') return null
      return {
        maxWidth: this.maxWidth || undefined,
        maxHeight: this.maxHeight || undefined,
      }
    },
  },
  methods: {
    getFileUrl,
    forceUpdate () {
      this.query = +new Date()
    },
  },
}
</script>

<style lang="less" scoped>
.c-async-file {
  max-width: 100%;

  &.image-wrap {
    position: relative;
    width: 100%;
    height: 100%;
    background: @normal-color center / cover no-repeat;

    .badge {
      position: absolute;
      bottom: 0;
      right: 0;
      color: #fff;
      font-size: @font-size-small;
      padding: 0 4px;

      &.gif {
        background-color: @success-color;
      }
    }
  }

  &.video {
    width: 100%;
  }
}
</style>
