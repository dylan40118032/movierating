<template>
  <div id="header" class="header">
    <nuxt-link class="main" to="/">
      <div class="title">
        <div>Movie</div>
        <div>Ranking</div>
      </div>
    </nuxt-link>
      <div class="label">
        <div>Copyright ©2020 Code by Dylan</div>
      </div>
  </div>
</template>

<script>
export default {
  data() {
    return {};
  },
  methods: {
    goPrevPage() {
      const vm = this;
      vm.$router.back();
    }
  }
};
</script>
<style lang="scss" scoped>
.header {
  display: flex;
  align-items: center;
  justify-content: space-around;
  height: 50px;
  background: #000;
  font-size: 1rem;
  position: relative;
  height: 150px;
  width: 100%;
  z-index: 105;
  bottom: 0;
  .title {
    font-size: 30px;
  }

  .label{
    display: flex;
    color:#aaa;
    margin-left: 50px;
    margin-top: 50px;
    div{
      margin-right: 15px;
    }
  }
  .main {
    display: flex;
    color: #aaa;
  }
}
</style>
