<template>
  <div class="c-notification-list">
    <aside>
      <ul class="nav">
        <li :class="{active: type === 'at'}" @click="$emit('update:type', 'at')">
          <svg class="icon lg"><use xlink:href="#icon-side-mention" /></svg>
          <span class="text">@我的</span>
          <i
            v-if="at.badge"
            class="badge"
            v-text="at.badge"
          />
        </li>
        <li :class="{active: type === 'comment'}" @click="$emit('update:type', 'comment')">
          <svg class="icon lg"><use xlink:href="#icon-side-msg" /></svg>
          <span class="text">评论我的</span>
          <i
            v-if="comment.badge"
            class="badge"
            v-text="comment.badge"
          />
        </li>
        <li :class="{active: type === 'like'}" @click="$emit('update:type', 'like')">
          <svg class="icon lg"><use xlink:href="#icon-side-like" /></svg>
          <span class="text">赞了我的</span>
          <i
            v-if="like.badge"
            class="badge"
            v-text="like.badge"
          />
        </li>
        <li :class="{active: type === 'system'}" @click="$emit('update:type', 'system')">
          <svg class="icon lg"><use xlink:href="#icon-side-notice" /></svg>
          <span class="text">系统通知</span>
          <i
            v-if="system.badge"
            class="badge"
            v-text="system.badge"
          />
        </li>
        <li :class="{active: type === 'audit'}" @click="$emit('update:type', 'audit')">
          <svg class="icon lg"><use xlink:href="#icon-side-auth" /></svg>
          <span class="text">审核通知</span>
          <i
            v-if="unreadAudits"
            class="badge"
            v-text="unreadAudits"
          />
        </li>
      </ul>
    </aside>

    <main>
      <h3 class="title">
        {{ computedType.title }}
        <div v-if="type === 'audit'" class="audit-selector">
          <Select v-model="audit" size="small">
            <Option
              v-for="(label, name) in auditTypeMap"
              :key="name"
              :value="name"
              :label="label"
            />
          </Select>
        </div>
      </h3>

      <div class="list-container">
        <Component :is="computedType.component" :audit-type="audit" />
      </div>
    </main>
  </div>
</template>

<script>
import NotificationListAtList from './NotificationListAtList.vue'
import NotificationListLikeList from './NotificationListLikeList.vue'
import NotificationListCommentList from './NotificationListCommentList.vue'
import NotificationListSystemList from './NotificationListSystemList.vue'
import NotificationListAuditList from './NotificationListAuditList.vue'
import { mapState, mapGetters } from 'vuex'

const typeMap = {
  at: { title: 'at我的', component: NotificationListAtList },
  like: { title: '赞我的', component: NotificationListLikeList },
  comment: { title: '评论我的', component: NotificationListCommentList },
  system: { title: '系统消息', component: NotificationListSystemList },
  audit: { title: '审核通知', component: NotificationListAuditList },
}

const auditTypeMap = {
  feedCommentPinned: '动态评论置顶',
  newsCommentPinned: '资讯评论置顶',
  postCommentPinned: '帖子评论置顶',
  postPinned: '帖子置顶',
  // groupJoin: '圈子加入申请',
}

export default {
  name: 'NotificationList',
  props: {
    type: { type: String, default: 'comment' },
    auditType: { type: String, default: 'feedCommentPinned' },
  },
  data () {
    return {
      auditTypeMap,
    }
  },
  computed: {
    ...mapState('notification', ['at', 'like', 'comment', 'system']),
    ...mapGetters('notification', ['unreadAudits']),
    audit: {
      get () {
        return this.auditType
      },
      set (type) {
        this.$emit('update:audit-type', type)
      },
    },
    computedType () {
      return typeMap[this.type]
    },
  },
}
</script>

<style lang="less" scoped>
.c-notification-list {
  display: flex;
  height: 80vh;
  max-height: 1200px;
  min-height: 400px;

  aside {
    flex: none;
    width: 160px;
    background-color: @background-color-base;
  }

  .nav {
    > li {
      display: flex;
      align-items: center;
      padding: 0 1em;
      height: 48px;
      cursor: pointer;

      &:hover {
        background-color: @border-color-base;
      }

      &.active {
        background-color: @btn-disable-color;
      }

      .text {
        margin-right: auto;
      }

      .icon {
        margin-right: .5em;
      }

      i.badge {
        border: none;
      }
    }
  }

  main {
    display: flex;
    flex-direction: column;
    flex: auto;
    overflow: hidden;

    .title {
      flex: none;
      display: flex;
      align-items: center;
      height: 48px;
      padding: 0 1em;
      .border(bottom);
    }
  }

  .list-container {
    flex: auto;
    overflow: auto;
  }

  .audit-selector {
    font-size: @font-size-base;
    font-weight: normal;
    margin-left: 16px;
  }
}
</style>

<style lang="less">
.c-notification-list {
  .c-notification-list-item {
    .list {
      > li {
        display: flex;
        padding: 16px;
        .border(bottom);

        .c-avatar {
          flex: none;
          margin-right: 16px;
        }

        .info {
          flex: auto;

          > time {
            color: @disabled-color;
            font-size: @font-size-small;
          }

          .c-reference {
            margin-top: 8px;
            font-size: 80%;
          }
        }
      }
    }
  }
}
</style>
