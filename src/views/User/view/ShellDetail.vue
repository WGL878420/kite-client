<template>
  <client-only>
    <section class="layout-content detail-lay">
      <div class="container  box-container">
        <div class="row">
          <div class="col-xs-12 col-sm-8 col-md-8 main">
            <div class="client-card pd20">
              <h3>贝壳明细</h3>
              <div class="amount">
                余额：<span class="amount-num">{{ personalInfo.user_info.shell_balance || 0 }}
                </span>
              </div>

              <table class="table-view">
                <tr>
                  <td style="width:20%"
                      class="hd">时间</td>
                  <td style="width:20%"
                      class="hd">类型</td>
                  <td style="width:20%"
                      class="hd">数额</td>
                  <td style="width:20%"
                      class="hd">余额</td>
                  <td style="width:20%"
                      class="hd">描述</td>
                </tr>
                <tr v-for="(detailItem, key) in detail.list"
                    :key="key">
                  <td style="width:20%">{{ detailItem.create_dt }}</td>
                  <td style="width:20%">{{ detailItem.actionText }}</td>
                  <td style="width:20%">
                    <span class="amount"
                          :class="
                        virtualPlusLess.plus === detailItem.plus_less
                          ? 'plus'
                          : 'less'
                      ">
                      {{
                        virtualPlusLessText[detailItem.plus_less] +
                          detailItem.amount
                      }}
                    </span>
                  </td>
                  <td style="width:20%">
                    <span class="balance">
                      {{ detailItem.balance }}
                    </span>
                  </td>
                  <td style="width:20%">
                    {{ detailItem.actionText + detailItem.typeText }}
                    <router-link v-if="detailItem.type === modelName.article"
                                 :to="{
                        name: 'article',
                        params: { aid: detailItem.modelInfo.aid }
                      }">{{ detailItem.modelInfo.title }}</router-link>
                    <router-link v-if="detailItem.type === modelName.dynamic"
                                 :to="{
                        name: 'dynamicView',
                        params: { dynamicId: detailItem.modelInfo.id }
                      }">{{ detailItem.modelInfo.content }}</router-link>
                    <router-link v-if="detailItem.type === modelName.books"
                                 :to="{
                        name: 'book',
                        params: { books_id: detailItem.modelInfo.books_id }
                      }">{{ detailItem.modelInfo.title }}</router-link>
                    <router-link v-if="detailItem.type === modelName.article_annex"
                                 :to="{
                        name: 'article',
                        params: {aid: detailItem.modelInfo.aid }
                      }">{{ detailItem.modelInfo.title }}</router-link>
                    <router-link v-if="detailItem.type === modelName.book"
                                 :to="{
                        name: 'BookView',
                        params: {
                          books_id: detailItem.modelInfo.books_id,
                          book_id: detailItem.modelInfo.book_id
                        }
                      }">{{ detailItem.modelInfo.title }}</router-link>

                    <a style="color:#666"
                       href="javascript:;"
                       v-if="detailItem.type === modelName.chat_message">{{ detailItem.modelInfo.content }}</a>
                  </td>
                </tr>
              </table>

              <Page :total="Number(detail.count)"
                    :pageSize="Number(detail.pageSize)"
                    :page="Number(detail.page) || 1"
                    @pageChange="pageChange"></Page>
            </div>
          </div>

          <div class="col-xs-12 col-sm-4 col-md-4 box-aside">
            <UserAside />
          </div>
        </div>
      </div>
    </section>
  </client-only>
</template>

<script>
import UserAside from '../view/UserAside'
import { mapState } from 'vuex'
import ClientOnly from 'vue-client-only'
import { Page, faceQQ, Dropdown } from '@components'
import {
  userMessageAction,
  virtualPlusLess,
  virtualPlusLessText,
  modelName
} from '@utils/constant'

export default {
  name: 'Personal',
  metaInfo () {
    return {
      title: '个人中心',
      htmlAttrs: {
        lang: 'zh'
      }
    }
  },
  data () {
    return {
      virtualPlusLess,
      virtualPlusLessText,
      modelName,
      detail: {
        list: [],
        count: 0,
        page: 1,
        pageSize: 10
      }
    }
  },
  mounted () {
    if (!this.personalInfo.islogin) {
      this.$router.push({ name: 'signIn' })
    }
    this.getVirtualList()
  },
  methods: {
    pageChange (val) {
      this.detail.page = val
      this.getVirtualList()
    },
    getVirtualList () {
      this.$store
        .dispatch('virtual/GET_VIRTUAL_LIST', {
          uid: this.personalInfo.user.uid,
          page: this.detail.page,
          pageSize: this.detail.pageSize
        })
        .then(result => {
          this.detail = result.data
        })
    }
  },
  computed: {
    ...mapState(['personalInfo', 'user']) // personalInfo:个人信息  user:登录后的个人信息当前用户
  },
  components: {
    UserAside,
    ClientOnly,
    Page
  }
}
</script>

<style scoped lang="scss">
.detail-lay {
  .amount {
    margin-top: 10px;
    margin-bottom: 10px;
    .amount-num {
      color: #0aa31c;
      font-weight: bold;
      font-size: 16px;
    }
  }
  .table-view {
    width: 100%;
    .hd {
      font-weight: bold;
    }
    tr {
      border-bottom: 1px solid #f3f3f3;
    }
    td {
      font-size: 14px;
      padding: 10px 0;
      .plus {
        color: #0aa31c;
      }
      .less {
        color: #ff3c00;
      }
      .balance,
      .amount {
        font-weight: bold;
      }
      .balance {
        color: #0aa31c;
      }
      a {
        font-size: 14px;
        color: #41b883;
        text-decoration: underline;
      }
    }
  }
}
</style>
