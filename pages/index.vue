<template>
  <div class="container">
    <header :noback="true"></header>
    <div v-if="loading" class="loading">
      <a-spin>
        <a-icon slot="indicator" type="loading" style="font-size:50px" spin />
      </a-spin>
    </div>
    <!-- 跑馬燈 -->
    <a-carousel id="carousel" v-else class="carousel" autoplay effect="fade">
      <div
        v-for="(item, index) in nowPlayingData"
        :key="index"
        class="carousel-item"
        @click="goDetail(item)"
      >
        <div class="left-area">
          <img
            v-if="item.backdrop_path"
            :src="`https://image.tmdb.org/t/p/w500/${item.backdrop_path}`"
          />
        </div>
        <div class="right-area">
          <div class="vote-top">{{ item.vote_average && item.vote_average.toFixed(1) }}/10</div>
          <div class="top">
            <div class="title">{{ item.original_title }}</div>
            <div class="type">
              <span v-for="(type, index2) in item.genre_ids" :key="index2">
                {{
                  typeData
                    .filter(e => e.id === type)
                    .map(e => e.name)
                    .toString()
                }}
                <span v-if="index2 !== item.genre_ids.length - 1">|</span>
              </span>
            </div>
          </div>
          <div class="bottom">
            <div class="vote">Popularity: {{ item.popularity }}</div>
            <div class="vote">Vote Count: {{ item.vote_count }}</div>
            <div class="vote">Release Date: {{ item.release_date }}</div>
          </div>
        </div>
      </div>
    </a-carousel>
    <div class="movie">
      <!-- 熱門 -->
      <div class="row-popular" id="popular">
        <div class="swiper-title">Popular</div>
        <div v-swiper:mySwiper="swiperOption" class="swiper-container">
          <div class="swiper-wrapper">
            <swiper-slide v-for="(item, index) in popularData" :key="index">
              <div class="rating">{{ index + 1 }}</div>
              <div class="show-title">{{ item.original_title }}</div>
              <img
                v-if="item.backdrop_path"
                :src="`https://image.tmdb.org/t/p/w500/${item.backdrop_path}`"
              />
              <div class="bg-text" @click="goDetail(item)">
                <div class="type">
                  <span v-for="(type, index2) in item.genre_ids" :key="index2">
                    {{
                      typeData
                        .filter(e => e.id === type)
                        .map(e => e.name)
                        .toString()
                    }}
                    <span v-if="index2 !== item.genre_ids.length - 1">|</span>
                  </span>
                </div>
              </div>
            </swiper-slide>
          </div>
        </div>
      </div>
      <div class="row-upcoming" id="upcoming">
        <div class="swiper-title">Upcoming</div>
        <div v-swiper:mySwiper2="swiperOption2" class="swiper-container">
          <div class="swiper-wrapper">
            <swiper-slide v-for="(item, index) in upcomingData" :key="index">
              <img
                v-if="item.backdrop_path"
                :src="`https://image.tmdb.org/t/p/w500/${item.backdrop_path}`"
              />
              <div class="bg-text" @click="goDetail(item)">
                <div>{{ item.original_title }}</div>
                <div class="type">
                  <span v-for="(type, index2) in item.genre_ids" :key="index2">
                    {{
                      typeData
                        .filter(e => e.id === type)
                        .map(e => e.name)
                        .toString()
                    }}
                    <span v-if="index2 !== item.genre_ids.length - 1">|</span>
                  </span>
                </div>
              </div>
            </swiper-slide>
          </div>
        </div>
      </div>
      <div class="row-playing" id="playing">
        <div class="swiper-title">Now Playing</div>
        <div v-swiper:mySwiper3="swiperOption2" class="swiper-container">
          <div class="swiper-wrapper">
            <swiper-slide v-for="(item, index) in nowPlayingData" :key="index">
              <img
                v-if="item.backdrop_path"
                :src="`https://image.tmdb.org/t/p/w500/${item.backdrop_path}`"
              />
              <div class="bg-text" @click="goDetail(item)">
                <div>{{ item.original_title }}</div>
                <div class="type">
                  <span v-for="(type, index2) in item.genre_ids" :key="index2">
                    {{
                      typeData
                        .filter(e => e.id === type)
                        .map(e => e.name)
                        .toString()
                    }}
                    <span v-if="index2 !== item.genre_ids.length - 1">|</span>
                  </span>
                </div>
              </div>
            </swiper-slide>
          </div>
        </div>
      </div>
      <div class="row-rate" id="rate">
        <div class="swiper-title">Top Rate</div>
        <div v-swiper:mySwiper4="swiperOption2" class="swiper-container">
          <div class="swiper-wrapper">
            <swiper-slide v-for="(item, index) in topRateData" :key="index">
              <img
                v-if="item.backdrop_path"
                :src="`https://image.tmdb.org/t/p/w500/${item.backdrop_path}`"
              />
              <div class="bg-text" @click="goDetail(item)">
                <div>{{ item.original_title }}</div>
                <div class="type">
                  <span v-for="(type, index2) in item.genre_ids" :key="index2">
                    {{
                      typeData
                        .filter(e => e.id === type)
                        .map(e => e.name)
                        .toString()
                    }}
                    <span v-if="index2 !== item.genre_ids.length - 1">|</span>
                  </span>
                </div>
              </div>
            </swiper-slide>
          </div>
        </div>
      </div>
    </div>
    <a-drawer
      :title="data.title"
      placement="right"
      :closable="true"
      :visible="showDetail"
      @close="showDetail = false"
    >
      <detail
        @close="showDetail = false"
        v-if="showDetail"
        :typeData="typeData"
        :data="data"
      ></detail>
    </a-drawer>
  </div>
</template>

<script>
import axios from "axios";
import { Swiper, SwiperSlide } from "vue-awesome-swiper";
import "swiper/swiper-bundle.css";
import Detail from "@/components/Detail";
import Header from "@/components/Header";

export default {
  components: {
    Swiper,
    SwiperSlide,
    Detail,
    Header
  },
  data() {
    return {
      apiKeyT: "0f79586eb9d92afa2b7266f7928b055c",
      upcomingData: {},
      popularData: {},
      nowPlayingData: {},
      topRateData: {},
      queryData: {},
      data: {},
      typeData: [],
      loading: true,
      showDetail: false,
      queryText: "",
      fullWidth: 0,
      swiperOption: {
        autoplay: 3000,
        grabCursor: true,
        slidesPerView: 3,
        slidesPerGroup: 3,
        spaceBetween: 100,
        paginationClickable: true
      },
      swiperOption2: {
        autoplay: 3000,
        grabCursor: true,
        slidesPerView: 5,
        slidesPerGroup: 5,
        spaceBetween: 30,
        paginationClickable: true
      }
    };
  },
  async mounted() {
    const vm = this;
    await vm.getUpcomingData();
    await vm.getPopularData();
    await vm.getNowPlayingData();
    await vm.getTopRateData();
    await vm.getType();
    vm.loading = false;
    setTimeout(() => {
      document.querySelector("#carousel").classList.add("active");
      document.querySelector("#popular").classList.add("active");
    }, 500);

    window.addEventListener("scroll", () => {
      const top1 = document.documentElement.scrollTop;
      const top2 = document.querySelector("#upcoming").offsetTop;
      const top3 = document.querySelector("#playing").offsetTop;
      const top4 = document.querySelector("#rate").offsetTop;
      const h = window.screen.height;
      // --- upcoming是否顯示 ---
      if (top1 + h / 2 > top2) {
        setTimeout(() => {
          document.querySelector("#upcoming").classList.add("active");
        }, 50);
      }
      // --- playing是否顯示 ---
      if (top1 + h / 2 > top3) {
        setTimeout(() => {
          document.querySelector("#playing").classList.add("active");
        }, 50);
      }
      // --- Rate是否顯示 ---
      if (top1 + h / 2 > top4) {
        setTimeout(() => {
          document.querySelector("#rate").classList.add("active");
        }, 50);
      }
    });
  },
  methods: {
    goDetail(item) {
      const vm = this;
      vm.data = item;
      vm.showDetail = true;
    },
    async getType() {
      const vm = this;
      const result = await axios.get(
        `https://api.themoviedb.org/3/genre/movie/list?api_key=0f79586eb9d92afa2b7266f7928b055c&language=en-US`
      );
      vm.typeData = result.data.genres;
    },
    async getUpcomingData() {
      const vm = this;
      const result = await axios.get(
        `https://api.themoviedb.org/3/movie/upcoming?api_key=0f79586eb9d92afa2b7266f7928b055c&language=en-US&page=1&region=US`
      );
      vm.upcomingData = result.data.results.filter(
        e => e.backdrop_path !== null
      );
    },
    async getPopularData() {
      const vm = this;
      const result = await axios.get(
        `https://api.themoviedb.org/3/movie/popular?api_key=0f79586eb9d92afa2b7266f7928b055c&language=en-US&page=1&region=US`
      );
      vm.popularData = result.data.results.filter(
        e => e.backdrop_path !== null
      );
    },
    async getNowPlayingData() {
      const vm = this;
      const result = await axios.get(
        `https://api.themoviedb.org/3/movie/now_playing?api_key=0f79586eb9d92afa2b7266f7928b055c&language=en-US&page=1&region=US`
      );
      vm.nowPlayingData = result.data.results.filter(
        e => e.backdrop_path !== null
      );
    },
    async getTopRateData() {
      const vm = this;
      const result = await axios.get(
        `https://api.themoviedb.org/3/movie/top_rated?api_key=0f79586eb9d92afa2b7266f7928b055c&language=en-US&page=1&region=US`
      );
      vm.topRateData = result.data.results.filter(
        e => e.backdrop_path !== null
      );
    } /*
    async query() {
      const vm = this;
      const result = await axios.post(
        `https://api.themoviedb.org/3/search/movie?api_key=0f79586eb9d92afa2b7266f7928b055c&query=${vm.queryText}`
      );
      vm.queryData =  result.data.results.filter(
        e => e.backdrop_path !== null
      );
    } */
  }
};
</script>

<style lang="scss" scoped>
.container {
  margin: 0 auto;
  height: 100%;
  background: #000;
  padding-bottom: 50px;
}
.loading {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100vh;
  color: #fff;
}
/deep/ .slick-dots {
  button {
    height: 10px !important;
    width: 10px !important;
    border-radius: 50% !important;
    margin: 0 2px !important;
  }
}
.carousel {
  width: 100%;
  padding: 50px 0 0;
  background: rgba(0, 0, 0, 0.7);
  color: #fff;
  height: 100%;
  transition: transform 1s, opacity 2s;
  opacity: 0;
  transform: translateY(-500px);
  cursor: pointer;

  &.active {
    opacity: 1;
    transform: translateY(0);
  }

  .carousel-item {
    display: flex !important;
    flex-wrap: nowrap;
    height: 500px;
    position: relative;
    width: 100%;

    &::before {
      position: absolute;
      content: "";
      top: -30px;
      right: -30px;
      height: 125px;
      width: 130px;
      border-radius: 20%;
      background: #f33;
      z-index: 1;
    }

    .left-area {
      flex-basis: 60%;
      height: 100%;

      img {
        height: 100%;
        width: 100%;
      }
    }

    .right-area {
      padding: 30px;
      flex-basis: 40%;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      position: relative;
      background: #333;

      .vote-top {
        position: absolute;
        right: 5px;
        top: 25px;
        font-size: 30px;
        color: #fff;
        font-weight: 600;
        z-index: 2;
      }
      .title {
        font-size: 40px;
        font-family: "Bangers", cursive;
      }
      .type {
        font-size: 14px;
      }
      .bottom {
        margin-bottom: 20px;
        font-size: 20px;
      }
    }
  }
}
.swiper-title {
  font-size: 30px;
  font-weight: 600;
  margin: 30px;
  color: #fff;
}
.swiper-container {
  width: 100%;
  height: 100%;
}
.row-popular {
  opacity: 0;
  transition: all 1s;
  transform: translateX(-2500px);

  &.active {
    opacity: 1;
    transform: translateX(0);
  }
}

.row-upcoming {
  opacity: 0;
  transition: all 1s;
  transform: translateX(2500px);

  &.active {
    opacity: 1;
    transform: translateX(0);
  }
}
.row-playing {
  opacity: 0;
  transition: all 1s;
  transform: translateX(-2500px);

  &.active {
    opacity: 1;
    transform: translateX(0);
  }
}
.row-rate {
  opacity: 0;
  transition: all 1s;
  transform: translateX(2500px);

  &.active {
    opacity: 1;
    transform: translateX(0);
  }
}
.swiper-slide {
  position: relative;

  img {
    width: 100%;
    height: 100%;
  }

  .show-title {
    position: absolute;
    color: #fff;
    font-size: 26px;
    left: 20px;
    bottom: 20px;
    font-family: "Bangers", cursive;
    font-family: "Krona One", sans-serif;
    z-index: 10;
    text-shadow: 0 0 5px #000;
  }

  .rating {
    position: absolute;
    color: #fff;
    font-size: 150px;
    right: -60px;
    bottom: -50px;
    font-family: "Diplomata", cursive;
    z-index: 10;
    text-shadow: 0 0 5px #000;
  }

  .bg-text {
    display: flex;
    position: absolute;
    height: 100%;
    width: 100%;
    z-index: 1;
    background: rgba(0, 0, 0, 0.7);
    top: 0;
    color: #fff;
    font-size: 20px;
    justify-content: center;
    text-align: center;
    align-items: center;
    flex-direction: column;
    opacity: 0;
    transition: all 0.3s;

    .type {
      margin-top: 10px;
    }
  }
}
.bg-text:hover {
  opacity: 1;
}
/deep/ .ant-drawer-content-wrapper {
  width: 100% !important;
  .ant-drawer-header {
    background: #000;
    border-radius: 0;
    .ant-drawer-title {
      color: #fff;
      margin-left: 80px;
      font-family: "Bangers", cursive;
      font-family: "Krona One", sans-serif;
    }
    svg {
      color: #fff;
    }
  }
  .ant-drawer-body {
    padding: 0 !important;
  }
  .ant-drawer-header {
    z-index: 100;
    width: 100%;
    position: fixed;
  }
}
</style>
