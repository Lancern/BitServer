<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12" lg="11" xl="9">
        <v-breadcrumbs class="pl-0 pt-0 py-3" :items="items" divider=">">
          <template v-slot:divider>
            <v-icon>mdi-chevron-right</v-icon>
          </template>
        </v-breadcrumbs>
        <v-card>
          <v-skeleton-loader :loading="skeleton" type="article">
            <v-card-title class="headline mb-2">
              {{ announcement.title }}
            </v-card-title>
            <v-card-subtitle>
              <nuxt-link :to="`/user/${announcement.author}`">
                <v-icon small left>
                  mdi-account
                </v-icon><span class="mr-2">{{ announcement.author }}</span>
              </nuxt-link><br class="hidden-sm-and-up">
              <v-icon small left>
                mdi-calendar
              </v-icon><span class="mr-2">{{ announcement.creationTime }}</span><br class="hidden-sm-and-up">
              <v-icon small left>
                mdi-square-edit-outline
              </v-icon>{{ announcement.lastUpdateTime }}
            </v-card-subtitle>
            <v-card-text>
              <div class="content">
                <div v-katex:auto v-highlight class="markdown-body" v-html="announcement.content" />
              </div>
            </v-card-text>
          </v-skeleton-loader>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
import { mapActions } from 'vuex'
import api from '@/plugins/utils/api'
import common from '@/plugins/utils/common'

export default {
  data () {
    return {
      items: [
        {
          text: '讨论'
        },
        {
          text: '全局板块',
          exact: true,
          to: '/article'
        },
        {
          text: '公告',
          to: '/article/' + this.$route.params.id
        }
      ],
      skeleton: false,
      announcement: {
        title: '',
        author: '',
        content: '',
        creationTime: '',
        lastUpdateTime: ''
      }
    }
  },
  mounted () {
    this.skeleton = true
    api.getAnnouncementDetail(this.$route.params.id).then((res) => {
      this.announcement.title = res.data.title
      this.announcement.author = res.data.author
      this.announcement.creationTime = common.getTimeFormat(res.data.creationTime)
      this.announcement.lastUpdateTime = common.getTimeFormat(res.data.lastUpdateTime)
      this.announcement.content = common.md.render(res.data.content)
      this.skeleton = false
    }).catch(() => {
      this.newToast({
        text: '获取内容失败!',
        color: 'error',
        icon: 'mdi-alert'
      })
    })
  },
  methods: {
    ...mapActions(['newToast'])
  }
}
</script>
