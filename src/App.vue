<template>
  <div id="app" ref="stage">
    <div class="bg" :style="{backgroundImage: bg}"></div>
    <div v-for="(value,index) in imgData" ref="imgs" class="imgCom" :style="imgStyle[index]" @click.stop.prevent="changeCenter(index)">
      <figureMy :srcMy="value.imgName" :title="value.title" :desc="value.desc" class="com" ref="hhh"></figureMy>
    </div>
    <bar :num="imgData.length" class="bar" @goto="changeCenter" ref="bar"></bar>
    <div class="preload" v-show="show">
      <div class="loading">
        <div class="circle1"></div>
        <div class="circle2"></div>
      </div>
      {{process}}
    </div>
  </div>
</template>
<script>
import figureMy from './components/figure/figure';
import bar from './components/bar/bar';
import './common/less/base.less';
import dataOld from '../data.json';
const bg = require('./common/img/bg.jpg')
//import './common/less/bg.less';
export default {
  name: 'app',
  data() {
    return {
      imgData: [],
      dataOld: dataOld,
      centerIndex: 0,
      bg: 'url('+bg+')',
      process: '0%',
      show: true,
      imgPath: [],
      // 中心图片的位置
      centerPos: {
        left: 0,
        top: 0
      },
      // 水平方向的取值范围
      hPosRange: {
        leftSecX: [0, 0],
        rightSecX: [0, 0],
        y: [0, 0]
      },
      // 垂直方向的取值范围
      vPosRange: {
        x: [0, 0],
        topY:[0, 0]
      }
    };
  },
  components: {
    figureMy,
    bar
  },
  created: function () {
    this.$nextTick(function(){
      this._init();
      this.preload(this.imgPath);
    });
  },
  methods: {
    _init: function() {
      const stageDOM = this.$refs.stage;
      const stageW = parseInt(stageDOM.scrollWidth);
      const stageH = parseInt(stageDOM.scrollHeight);

      const halfStageW = Math.ceil(stageW/2);
      const halfStageH = Math.ceil(stageH/2);
      let data = [];
      for (let i=0; i<this.dataOld.length; i++){
        let singleImgData = this.dataOld[i];
        singleImgData.imgName = require('./common/img/' + this.dataOld[i].imgName);
        this.imgPath.push(singleImgData.imgName);
        data[i] = singleImgData;
      }

      this.imgPath.push(bg);

      this.imgData = data;
      this.$nextTick(function () {
        const imgDOM = this.$refs.imgs;
        const ImgW = parseInt(imgDOM[0].scrollWidth);
        const ImgH = parseInt(imgDOM[0].scrollHeight);
        const halfImgW = Math.ceil(ImgW/2);
        const halfImgH = Math.ceil(ImgH/2);
        // 图片中心位置定位赋值
        this.centerPos.left = halfStageW - halfImgW;

        this.centerPos.top = halfStageH - halfImgH;
        // 左右区域取值范围赋值
        this.hPosRange.leftSecX = [-halfImgW, halfStageW - halfImgW * 3];
        this.hPosRange.rightSecX = [halfStageW + halfImgW, 2 * halfStageW - halfImgW];
        this.hPosRange.y = [-halfImgH, 2 * halfStageH - halfImgH];
        // 上部分的取值范围
        this.vPosRange.x = [halfStageW - 2 * halfImgW, halfStageW];
        this.vPosRange.topY = [-halfImgW, halfStageH - 3 * halfImgH];
//        console.log(this.vPosRange);
      });
    },
    preload: function(img) {
      const imgs = typeof img === 'string' ? [img] : img;
      const len = imgs.length;
      let count = 0;
      for (let i = 0; i < len; i++) {
        let imgObj = new Image();
        imgObj.onload = imgObj.onerror = function (){
          count++;
          if (count >= len){
            this.show = false;
            return;
          }
          this.process = ((count/len)*100).toFixed(2) + '%';
        }.bind(this);
        imgObj.src = imgs[i];
      }
    },
    /**
     * 获得指定范围内的随机证书
     * @param min (num) 指定范围的下边界
     * @param max (num) 指定范围的上边界
     * @returns {number}
     */
    getRangeNum: function (min, max) {
      return Math.ceil(Math.random() * (max - min) + min);
    },
    /**
     * 点击修改中心图片
     * 或者点击中心图片时翻转图片
     * @param centerIndex 当前点击的图片索引
     */
    changeCenter: function(index) {
      if (this.centerIndex === index) {
        this.$refs.hhh[index].fanzhuan();
        this.$refs.bar.changeIndexControl();
        return false;
      }
      this.$refs.hhh[this.centerIndex].huifuCenter();
      this.$refs.bar.resetIndexControl();
      this.centerIndex = index;
      this.$refs.bar.changeCenter(this.centerIndex);
    }
  },
  computed: {
    imgStyle: function () {
      let arr = [];
      const len = this.imgData.length;
      for (let i = 0; i<len; i++){
        if (i === this.centerIndex){
          arr[i] = {
            left : this.centerPos.left + 'px',
            top : this.centerPos.top + 'px',
            transform : 'rotate(0)',
            zIndex: '100'
          };
        } else if (i === Math.floor(len/2)){
          arr[i] = {
            left : this.getRangeNum(...this.vPosRange.x) + 'px',
            top : this.getRangeNum(...this.vPosRange.topY) + 'px',
            transform : 'rotate('+this.getRangeNum(-30, 30)+'deg)'
          }
        } else if (i < Math.floor(len/2)) {
          arr[i] = {
            left : this.getRangeNum(...this.hPosRange.leftSecX) + 'px',
            top : this.getRangeNum(...this.hPosRange.y) + 'px',
            transform : 'rotate('+this.getRangeNum(-30, 30)+'deg)'
          };
        } else {
          arr[i] = {
            left : this.getRangeNum(...this.hPosRange.rightSecX)+ 'px',
            top : this.getRangeNum(...this.hPosRange.y) + 'px',
            transform : 'rotate('+this.getRangeNum(-30, 30)+'deg)'
          };
        }
      }
      return arr;
    }
  }
}
</script>

<style lang="less" rel="stylesheet/less" scoped>
  @time: .8s;
  @timeLoading: 2s;
  @width: 5rem;
#app {
  width: 100%;
  height: 100%;
  overflow: hidden;
  position: fixed;
  top:0;
  left:0;
  .bg{
    /*background-image: url('./common/img/bg.jpg');*/
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top:0;
    /*background-image: url('./common/img/bg.jpg');*/
    background-size: cover;
    filter: blur(10px);
  }
  .imgCom{
    position: absolute;
    transition : left @time ease-in-out, top @time ease-in-out, transform @time ease-in-out;
    transform: translate3d(0,0,0);
    cursor: pointer;
  }
  .bar{
    position: relative;
    display: table;
    margin: 0 auto;
    top: 91%;
  }
  .preload{
    width: 100%;
    height: 100%;
    position: fixed;
    left: 0;
    top:0;
    text-align: center;
    background-color: #eee;
    z-index: 200;
    font-size: 1.5rem;
    padding-top: 25rem;
    .loading{
      width: @width;
      height: @width;
      position: relative;
      margin: 0 auto;
      .circle2,.circle1{
        position: absolute;
        width: @width;
        height: @width;
        background-color: darkblue;
        border-radius: 50% 50%;
        animation-timing-function: ease-in-out;
      }
      .circle1{
        opacity: 0.7;
        animation: loading1 @timeLoading infinite;
      }
      .circle2{
        opacity: 0.9;
        transform: scale(0);
        animation: loading2 @timeLoading infinite;
      }
    }
  }
}
  @keyframes loading1{
    0%{  opacity: 0.7;  transform: scale(1);  }
    50%{  opacity: 0.9;  transform: scale(0);  }
  }
  @keyframes loading2{
    0%{  opacity: 0.9;  transform: scale(0);  }
    50%{  opacity: 0.7;  transform: scale(1);  }
  }
</style>
