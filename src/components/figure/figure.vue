<template>
    <figure class="figure">
      <img :src="srcMy" class="tupian" :alt="desc"/>
      <figcaption>
        <h2 class="desc">{{title}}</h2>
      </figcaption>
    </figure>
</template>

<script>
    export default {
      props: {
        srcMy: {
          type: String
        },
        desc: {
          type: String
        },
        title: {
          type: String
        }
      },
      data() {
        return {
        }
      }
    };
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" rel="stylesheet/less" scoped>
.figure{
  padding: 30px 0;
  width: 300px;
  background-color: #ffffff;
  margin: 0;
  .tupian{
    display: block;
    margin: 0 auto;
    width: 240px;
    height: 180px;
  }
  .desc{
    font-size: 15px;
    color: gray;
    text-align: center;
  }
}
</style>
