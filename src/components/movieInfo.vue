<template>
  <article class="main flex">
    <VuePerfectScrollbar>
      <Row style="width: 100%;">
        <Col
          :xs="{span: 22, offset: 1}"
          :sm="{span: 12, offset: 6}"
          :lg="{span: 12, offset: 6}"
        >
          <section>
            <header class="movie-details">
              <span class="title">{{ movie.name }}</span>
              <span class="date">{{ movie.release_date }}</span>
            </header>
            <div>
              <p>片長： {{ movie.runtime }}{{ $t('minute') }}</p>
              <p>導演： {{ movie.director }}</p>
            </div>
            <div style="margin: 20px 0;">
              {{ movie.description }}
            </div>
          </section>
          <section class="flex">
            <aside class="float-left fixed comment-block">
              <img :src="'https://image.tmdb.org/t/p/w185/' + movie.poster_path">
              分數 觀看 收藏
              <score-bar></score-bar>
              <div>
                <p>我的評論</p>
                <Input v-model="comment.value" type="textarea" :rows="5" placeholder="請寫下你的評論" />
                <Button class="submit float-right">送出</Button>
              </div>
            </aside>
            <div class="float-left stretch" style="margin-left: 36px;">
              <div>
                <Tabs class="simple-tab">
                  <TabPane label="演員">
                    <div v-if="movie.cast.length === 0">
                      沒有相關資訊:(
                    </div>
                    <div v-else>
                      <span v-for="(item, key) in movie.cast" :key="key">{{item}}</span>
                    </div>
                  </TabPane>
                  <TabPane label="劇組">
                    <div v-if="movie.crew.length === 0">
                      沒有相關資訊:(
                    </div>
                    <div v-else>
                      <span v-for="(item, key) in movie.crew" :key="key">{{item}}</span>
                    </div>
                  </TabPane>
                  <TabPane label="類型">
                    <div v-if="movie.type.length === 0">
                      沒有相關資訊:(
                    </div>
                    <div v-else>
                      <span v-for="(item, key) in movie.type" :key="key">{{item}}</span>
                    </div>
                  </TabPane>
                </Tabs>
              </div>
              <div>
                <Tabs class="simple-tab">
                  <TabPane label="熱門評論">
                    Coming soon ;-)
                  </TabPane>
                  <TabPane label="近期評論">
                    Coming soon ;-)
                  </TabPane>
                </Tabs>
              </div>
              <div>
                <Tabs class="simple-tab">
                  <TabPane label="相關作品">
                    Coming soon ;-)
                  </TabPane>
                </Tabs>
              </div>
            </div>
          </section>
        </Col>
      </Row>
    </VuePerfectScrollbar>
  </article>
</template>

<script>
import VuePerfectScrollbar from 'vue-perfect-scrollbar';
import scoreBar from './common/score-bar';

export default {
  name: 'movieInfo',
  components: {
    VuePerfectScrollbar,
    scoreBar
  },
  data() {
    return {
      movie: {
        id: this.$route.query.mid,
        name: '--',
        release_date: 'N/A',
        runtime: 0,
        director: 'N/A',
        description: 'N/A',
        poster_path: '',
        cast: [],
        crew: ['larry'],
        type: ['scary', 'action']
      },
      comment: {
        value: ''
      }
    }
  },
  created: function () {
    let vm = this

    vm.$axios
      .get('https://howing.co/api/v1/movies/' + vm.movie.id)
      .then(function (resp) {
        let _data = resp.data

        vm.movie.name = _data.title
        vm.movie.release_date = vm.$moment(_data.releaseDate).format('YYYY/MM')
        vm.movie.description = _data.overview
        vm.movie.poster_path = _data.posterPath
      }
    )
  }
}
</script>

<style lang="scss" scoped>
@import '~sass-rem';
@import '@/assets/scss/_variables.scss';
.movie-details {
  .title {
    font-size: rem(24px);
  }
  .date {
    font-size: rem(14px);
    margin-left: 10px;
  }
  &:before {
    content: '';
    height: 100%;
    display: inline-block;
    position: relative;
    vertical-align: middle;
  }
}
.comment-block {
  button {
    &.submit {
      margin-top: 8px;
    }
  }
}
.simple-tab {
  .ivu-tabs-tabpane {
    padding: 0px 5px 20px;
    color: $white;

    span {
      padding: 0 12px;
      margin-right: 10px;
      border-radius: 3px;
      background-color: rgba(255, 255, 255, 0.19);
      display: inline-block;
    }
  }
}
</style>
