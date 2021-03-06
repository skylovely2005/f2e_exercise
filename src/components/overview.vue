<template>
<article class="main flex">
  <VuePerfectScrollbar>
    <section class="horizonal-slide">
      <h2>熱門影集</h2>
      <div class="poster-gallery">
        <div class="pointer nav-to-left" @click="shift2('left', 'scroll1')"><i class="icomoon-arrow-left2"></i></div>
        <VuePerfectScrollbar ref="scroll1" class="carousel-list">
          <div v-for="(obj, key) in seriesData" :key="key" class="poster-block">
            <div class="image-box" @mouseenter="showHoverBox('seriesData', obj.id)" @mouseleave="hideHoverBox('seriesData', obj.id)">
              <img :src="'https://image.tmdb.org/t/p/w185/' + obj.poster_path">
              <div class="box-mask" v-show="obj.show">
                <div v-if="obj.overview">
                  <div class="title">{{ obj.title }}</div>
                  <div class="subtitle">subtitle</div>
                  <div class="details">{{ obj.overview }}</div>
                </div>
                <div v-else>
                  目前沒有相關資訊
                </div>
                <Button class="more" disabled><router-link :to="{name: 'movieInfo', query: {mid: obj.id}}">瀏覽更多</router-link></Button>
              </div>
            </div>
            <div class="mov-title">{{ obj.title }}</div>
            <score-bar></score-bar>
          </div>
        </VuePerfectScrollbar>
        <div class="pointer nav-to-right" @click="shift2('right', 'scroll1')"><i class="icomoon-arrow-right2"></i></div>
      </div>
    </section>
    <section class="horizonal-slide">
      <h2>熱門電影</h2>
      <div class="poster-gallery">
        <div class="pointer nav-to-left" @click="shift2('left', 'scroll2')"><i class="icomoon-arrow-left2"></i></div>
        <VuePerfectScrollbar ref="scroll2" class="carousel-list">
          <div v-for="(obj, key) in movieData" :key="key" class="poster-block">
            <div class="image-box" @mouseenter="showHoverBox('movieData', obj.id)" @mouseleave="hideHoverBox('movieData', obj.id)">
              <img :src="'https://image.tmdb.org/t/p/w185/' + obj.poster_path">
              <div class="box-mask" v-show="obj.show">
                <div v-if="obj.overview">
                  <div class="title">{{ obj.title }}</div>
                  <div class="subtitle">subtitle</div>
                  <div class="details">{{ obj.overview }}</div>
                </div>
                <div v-else>
                  目前沒有相關資訊
                </div>
                <Button class="more"><router-link :to="{name: 'movieInfo', query: {mid: obj.id}}">瀏覽更多</router-link></Button>
              </div>
            </div>
            <div class="mov-title">{{ obj.title }}</div>
            <score-bar></score-bar>
          </div>
        </VuePerfectScrollbar>
        <div class="pointer nav-to-right" @click="shift2('right', 'scroll2')"><i class="icomoon-arrow-right2"></i></div>
      </div>
    </section>
  </VuePerfectScrollbar>
</article>
</template>

<script>
import VuePerfectScrollbar from 'vue-perfect-scrollbar';
import scoreBar from './common/score-bar';

const RANK_NUM = 10

export default {
  name: 'overview',
  components: {
    VuePerfectScrollbar,
    scoreBar
  },
  data() {
    return {
      scrollHorizonDistance: 243,
      movieData: [],
      seriesData: []
    };
  },
  created: function () {
    let vm = this;

    vm.getData('seriesData', {page: 1, limit: RANK_NUM, sort_by: 'popularity.desc'});
    vm.getData('movieData', {page: 1, limit: RANK_NUM, sort_by: 'popularity.desc'});
  },
  computed: {
    RANK_NUM () {
      return RANK_NUM
    }
  },
  mounted () {},
  methods: {
    SDATA: function (info) {
      return {
        id: info.id,
        title: info.title,
        title_en: info.originalTitle,
        poster_path: info.posterPath,
        backdrop_path: info.backdropPath,
        popularity: info.popularity,
        overview: info.overview
      };
    },
    getData: function (targetType, p) {
      let vm = this;
      const typeMapping = {
        movieData: 'movies',
        seriesData: 'tv'
      };

      vm.$axios
      .get('https://howing.co/api/v1/' + typeMapping[targetType], {
        params: { page: p.page, limit: p.limit, sort_by: p.sort_by}
      })
      .then(function (resp) {
        let _data = resp.data;
        _.forEach(_data.results, function(val, key) {
          let sData = vm.SDATA(val);

          vm.$set(vm[targetType], key, sData);
          vm.$set(vm[targetType][key], 'show', false);
        });
      });
    },
    shift2: function (direction, target) {
      let vm = this
      let next_pos = -1
      let curr_pos = vm.$refs[target].$el.scrollLeft
      let pace = Math.floor(curr_pos / vm.scrollHorizonDistance)

      if (direction == 'left') {
        next_pos = (pace - 1) * vm.scrollHorizonDistance
      } else if (direction == 'right') {
        next_pos = (pace + 1) * vm.scrollHorizonDistance
      }
      vm.$refs[target].$el.scrollLeft = next_pos
    },
    showHoverBox: function (dataType, id) {
      let vm = this;
      const index = _.findIndex(vm[dataType], { 'id': id })

      this.$set(vm[dataType][index], 'show', true);
    },
    hideHoverBox: function (dataType, id) {
      let vm = this;
      const index = _.findIndex(vm[dataType], { 'id': id })

      vm.$set(vm[dataType][index], 'show', false);
    }
  }
}
</script>

<style lang="scss" scoped>
@import '~sass-rem';
@import '@/assets/scss/_variables.scss';
.horizonal-slide {
  h2 {
    font-size: rem(30px);
    margin: rem(24px) 13% rem(20px);
  }
}
.poster-gallery {
  $nav-to-btn-width: 110px;
  overflow-x: hidden;
  white-space: nowrap;
  background: rgba(233, 233, 233, 0.3);
  padding: 0 10px;
  position: relative;

  [class*="nav-to-"] {
    width: $nav-to-btn-width;
    height: 100%;
    top: 0;
    position: absolute;
    z-index: 1;
    font-size: rem(66px);
    color: rgba(255, 255, 255, 0.6);
    
    &:before {
      content: "";
      height: 100%;
      vertical-align: middle;
      display: inline-block;
    }
  }
  .nav-to-left {
    left: 0;
    text-align: right;
    background: linear-gradient(to right, rgba(30, 40, 49, 1) 0%,rgba(30, 40, 49, 0.01) 100%);
  }
  .nav-to-right {
    right: 0;
    background: linear-gradient(to right, rgba(30, 40, 49, 0.01) 0%,rgba(30, 40, 49, 1) 100%);
  }
  .carousel-list {
    margin: 0 $nav-to-btn-width - 34px;
  }
  .poster-block {
    $p-width: 195px;
    $p-margin: 8px;
    width: $p-width + ($p-margin * 2);
    margin: rem(12px) rem(16px);
    display: inline-block;
    border-radius: 2px;
    color: $darkBlue;
    background-color: rgba(221, 221, 221, 0.52);

    .image-box {
      position: relative;
      margin: $p-margin $p-margin 0;
      color: $white;

      img {
        width: $p-width;
        height: $p-width * 1.48;
        display: block;
        margin: auto;
      }
      .box-mask {
        position: absolute;
        top: 0; bottom: 0;
        right: 0; left: 0;
        background: rgba(0, 0, 0, 0.68);
        text-align: center;
        display: flex;
        flex-direction: column;

        > div {
          height: calc(100% - 45px);
          width: 100%;
          position: absolute;
          overflow: hidden;
          padding: 3px 7px 0;

          .title {
            font-size: rem(18px);
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
            text-align: left;
          }
          .subtitle {
            text-align: left;
          }
          .details {
            white-space: normal;
            text-align: justify;
          }
        }
        button.more {
          color: $black;
          font-size: rem(14px);
          background-color: #d2b706;
          border: none;
          position: absolute;
          bottom: 6px;
          left: 0; right: 0;
          margin: 0 auto;
          width: 86px;
        }
      }
    }
    .mov-title {
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      font-size: rem(18px);
      margin: 0 8px 6px;
      font-weight: 600;
    }
  }
}
</style>

<style lang="scss">
.progbar-container {
  .ivu-progress {
    display: flex;
  }
}
</style>
