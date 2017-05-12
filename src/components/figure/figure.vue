<template>
    <figure class="figure" :class="{isInverse: isInverse}">
      <img :src="srcMy" class="tupian" :alt="desc"/>
      <figcaption>
        <h2 class="desc">{{title}}</h2>
      </figcaption>
      <div class="back">
        <p>{{desc}}</p>
      </div>
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
          isInverse: false
        }
      },
      methods: {
        /**
         * 供父组件调用，实现子组件的翻转
         */
        fanzhuan: function () {
          this.isInverse = !this.isInverse
        },
        /**
         * 供父组件使用，将中间没有回复的图片回复
         */
        huifuCenter: function() {
          this.isInverse = false;
        }
      }
    };
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" rel="stylesheet/less" scoped>
  @time: .8s;
.figure{
  padding: 2rem 3rem;
  /*width: 300px;*/
  background-color: #ffffff;
  margin: 0;
  transition : opacity @time ease-in-out, transform @time ease-in-out;
  perspective: 1800px;
  .tupian{
    display: block;
    margin: 0 auto;
    width: 24rem;
    height: 16rem;
  }
  .desc{
    font-size: 1.5rem;
    color: gray;
    text-align: center;
  }
  .back{
    width: 100%;
    height: 100%;
    top:0;
    left: 0;
    position: absolute;
    text-align: center;
    font-size: 2rem;
    font-weight: 700;
    color: gray;
    opacity: 0;
    transition: opacity @time ease-in-out;
    background-color: #ffffff;
    transform: rotateY(180deg);
    box-sizing: border-box;
    p{
      padding: 7rem 3rem 3rem 3rem;
    }
  }
  &.isInverse{
    transform: rotateY(180deg);
    .back{
      opacity: 1;
    }
  }
}
</style>
