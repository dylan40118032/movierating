<template>
  <div class="section">
    <div class="back" @click="close()">Back</div>
    <div class="row-top">
      <div class="vote-top">
        {{ data.vote_average && data.vote_average.toFixed(1) }}/10
      </div>
      <div class="item top-left">
        <img
          v-if="data.backdrop_path"
          :src="`https://image.tmdb.org/t/p/w500/${data.backdrop_path}`"
        />
      </div>
      <div class="item top-right">
        <div>
          <div class="title">{{ data.original_title }}</div>
          <div class="type">
            <span v-for="(item, index) in data && data.genre_ids" :key="index">
              {{
                typeData
                  .filter(e => e.id === item)
                  .map(e => e.name)
                  .toString()
              }}
              <span v-if="index !== data.genre_ids.length - 1">|</span>
            </span>
          </div>
          <div class="date">{{ data.release_date }}</div>
          <div class="date" style="margin-top:10px">
            Popularity:
            <span class="green" :class="{ red: data.popularity > 60 }">{{
              data.popularity
            }}</span>
          </div>
          <div class="date">Vote Count: {{ data.vote_count }}</div>
        </div>
        <div>
          <div class="overview">{{ data.overview }}</div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="swiper-title">Cast</div>
      <div v-swiper:mySwiper="swiperOption" class="swiper-container">
        <div class="swiper-wrapper">
          <div v-if="castData && !castData.length">No Cast Data</div>
          <swiper-slide v-for="(item, index) in castData" :key="index">
            <img
              :src="`https://image.tmdb.org/t/p/w500${item.profile_path}`"
              @click.prevent="goCast(item)"
            />
            <div class="name">{{ item.character }}</div>
            <div class="name">as</div>
            <div class="name">{{ item.name }}</div>
          </swiper-slide>
        </div>
      </div>
    </div>
    <a-drawer
      title="Cast Detail"
      placement="right"
      :closable="true"
      :visible="showCast"
      @close="showCast = false"
    >
      <cast @close="showCast = false" v-if="showCast" :data="castDetail"></cast>
    </a-drawer>
  </div>
</template>

<script>
import axios from "axios";
import { Swiper, SwiperSlide } from "vue-awesome-swiper";
import "swiper/swiper-bundle.css";
import Cast from "@/components/Cast";

export default {
  props: {
    data: {
      type: Object,
      default: () => {}
    },
    typeData: {
      type: Array,
      default: () => []
    }
  },
  components: {
    Swiper,
    SwiperSlide,
    Cast
  },
  data() {
    return {
      apiKeyT: "0f79586eb9d92afa2b7266f7928b055c",
      castData: [],
      castDetail: {},
      showCast: false,
      swiperOption: {
        autoplay: 3000,
        slidesPerView: 5,
        slidesPerGroup: 5,
        spaceBetween: 100,
        paginationClickable: true
      }
    };
  },
  async mounted() {
    const vm = this;
    await vm.getCast();
  },
  methods: {
    close() {
      const vm = this;
      vm.$emit("close");
    },
    async getCast() {
      const vm = this;
      const result = await axios.get(
        `https://api.themoviedb.org/3/movie/${vm.data.id}/credits?api_key=0f79586eb9d92afa2b7266f7928b055c`
      );
      vm.castData = result.data.cast.filter(e => e.profile_path !== null);
    },
    goCast(item) {
      const vm = this;
      vm.castDetail = item;
      vm.showCast = true;
    }
  }
};
</script>

<style lang="scss" scoped>
.section {
  background: #fff;
  padding-top: 80px;
  .row-top {
    display: flex;
    position: relative;
    box-shadow: 0 0 5px #333;
    border-radius: 10px;
    background: #fff;
    margin: 0 20px;

    .vote-top {
      position: absolute;
      right: 10px;
      top: 25px;
      font-size: 26px;
      color: #fff;
      font-weight: 600;
      z-index: 2;
    }

    &::before {
      position: absolute;
      content: "";
      top: 0px;
      right: 0px;
      height: 100px;
      width: 100px;
      border-radius: 0 10px 0 20%;
      background: #f33;
      z-index: 1;
    }

    .top-left {
      flex-basis: 45%;

      img {
        width: 100%;
        height: 100%;
        border-radius: 10px 0 0 10px;
      }
    }
    .top-right {
      flex-basis: 50%;
      padding: 15px 0 15px 30px;
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      position: relative;

      .title {
        color: #000;
        font-weight: 600;
        font-size: 32px;
        font-family: "Bangers", cursive;
        font-family: "Krona One", sans-serif;
      }
      .date {
        font-size: 14px;
        font-family: "Bangers", cursive;
        font-family: "Krona One", sans-serif;
      }
      .type {
        font-size: 14px;
        font-family: "Bangers", cursive;
        font-family: "Krona One", sans-serif;
      }
      .overview {
        font-size: 16px;
      }
    }
  }
}
.swiper-slide:hover {
  transform: scale(1.1);
}
.swiper-container {
  padding: 30px;
}
.swiper-slide {
  display: flex;
  flex-direction: column;
  cursor: pointer;
  transition: 0.3s all;
  height: 100%;
  background: #fff;
  box-shadow: 0 0 2px #333;
  padding-bottom: 15px;
  border-radius: 15px 15px 0 0;

  img {
    width: 100%;
    height: 80%;
    border-radius: 15px 15px 0 0;
    margin-bottom: 15px;
  }
}
.swiper-title {
  font-size: 30px;
  font-weight: 600;
  margin: 30px 15px 0 30px;
  color: #333;
}
.name {
  font-size: 16px;
  font-weight: 600;
  text-align: center;
}
.green {
  color: #2f2;
}

.red {
  color: #f33;
}
.back {
  display: flex;
  z-index: 101;
  top: 12px;
  left: 20px;
  position: fixed;
  justify-content: center;
  width: 55px;
  padding: 4px 2px;
  border: 1px solid #fff;
  color: #fff;
  border-radius: 5px;
  cursor: pointer;
  transition: 0.3s all;

  &:hover {
    background: #fbfbfb;
    color: #000;
  }
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
    width: 100%;
    position: fixed;
    z-index: 100;
  }
}
</style>
