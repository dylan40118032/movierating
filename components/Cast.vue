<template>
  <div class="section">
    <div class="back" @click="close()">Back</div>

    <div v-if="loading" class="loading">
      <a-spin>
        <a-icon slot="indicator" type="loading" style="font-size:50px" spin />
      </a-spin>
    </div>
    <div v-else class="row">
      <div class="swiper-title">Cast Detail</div>
      <div class="wrapper">
        <div class="item">
          <div class="vote-top">
            {{ detail.popularity && detail.popularity.toFixed(3) }}
          </div>
          <div class="img-area">
            <img
              :src="`https://image.tmdb.org/t/p/w500${detail.profile_path}`"
            />
          </div>
          <div class="right-area">
            <div class="top">
              <div class="title">{{ detail.name }}</div>
              <div class="overview">{{ detail.birthday }}</div>
              <div class="overview">{{ detail.place_of_birth }}</div>
            </div>
            <div class="bottom">
              <div v-if="detail.biography" class="overview">{{ detail.biography }}</div>
              <div v-else class="overview">No more data for this cast</div>
            </div>
          </div>
        </div>
      </div>
      <div class="swiper-title">Acted Movie</div>
      <div class="wrapper">
        <div v-for="(item, index) in beforeMovie" :key="index" class="item">
          <div class="vote-top">
            {{ item.vote_average && item.vote_average.toFixed(1) }}/10
          </div>
          <div class="img-area">
            <img :src="`https://image.tmdb.org/t/p/w500${item.poster_path}`" />
          </div>
          <div class="right-area">
            <div class="top">
              <div class="title">
                {{
                  item.original_title ? item.original_title : item.original_name
                }}
              </div>
              <div class="overview">{{ item.release_date }}</div>
              <div class="name">
                Character: {{ item.character ? item.character : "No Data" }}
              </div>
            </div>
            <div class="bottom">
              <div class="popularity">Popularity: {{ item.popularity }}</div>
              <div class="vote-count">Vote Count: {{ item.vote_count }}</div>
              <div class="overview">{{ item.overview }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: {
    data: {
      type: Object,
      default: () => {}
    }
  },
  data() {
    return {
      apiKeyT: "0f79586eb9d92afa2b7266f7928b055c",
      loading: true,
      beforeMovie: [],
      detail: {}
    };
  },
  async mounted() {
    const vm = this;
    await vm.getBeforeMovie();
    await vm.getDetail();
    setTimeout(() => {
      vm.loading = false;
    }, 50);
  },
  methods: {
    close() {
      const vm = this;
      vm.$emit("close");
    },
    async getBeforeMovie() {
      const vm = this;
      const result = await axios.get(
        `https://api.themoviedb.org/3/person/${vm.data.id}/combined_credits?api_key=0f79586eb9d92afa2b7266f7928b055c&language=en-US`
      );
      vm.beforeMovie = result.data.cast.filter(e => e.poster_path !== null);
    },
    async getDetail() {
      const vm = this;
      const result = await axios.get(
        `https://api.themoviedb.org/3/person/${vm.data.id}?api_key=0f79586eb9d92afa2b7266f7928b055c&language=en-US`
      );
      vm.detail = result.data;
    }
  }
};
</script>

<style lang="scss" scoped>
.wrapper {
  font-size: 18px;
  background: #fff;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  margin: 35px;

  .item {
    margin: 0 30px 60px 30px;
    width: 100%;
    border-radius: 10px;
    display: flex;
    box-shadow: 0 0 5px #333;
    position: relative;

    .img-area {
      margin: 30px;
      flex-basis: 35%;
    }

    .right-area {
      flex-basis: 50%;
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      padding: 0 30px;

      .title {
        font-size: 36px;
        font-weight: 600;
        color: #000;
      }
      .name {
        font-size: 20px;
        color: #000;
        font-weight: 600;
        margin-top: 10px;
      }
      .popularity {
        font-weight: 600;
      }
      .vote-count {
        font-weight: 600;
      }
      .overview {
        font-weight: 600;
      }
    }

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
  }
}

.swiper-title {
  font-size: 30px;
  font-weight: 600;
  margin: 0 15px 0 30px;
  color: #333;
}
.back {
  display: flex;
  top: 12px;
  z-index: 101;
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
.loading {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100vh;
  color: #fff;
}
</style>
