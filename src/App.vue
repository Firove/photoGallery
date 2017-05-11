<template>
  <div id="app" ref="stage">
    <div class="bg"></div>
    <div v-for="(value,index) in imgData" ref="imgs" class="imgCom" :style="imgStyle[index]" @click.stop.prevent="changeCenter(index)">
      <figureMy :srcMy="value.imgName" :title="value.title" :desc="value.desc" class="com" ref="hhh"></figureMy>
    </div>
    <bar :num="imgData.length" class="bar" @goto="changeCenter" ref="bar"></bar>
  </div>
</template>
<script>
import figureMy from './components/figure/figure';
import bar from './components/bar/bar';
import './common/less/base.less';
import dataOld from '../data.json';

export default {
  name: 'app',
  data() {
    return {
      imgData: [],
      dataOld: dataOld,
      centerIndex: 0,
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
//      this.rearrange(this.centerIndex);
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
        data[i] = singleImgData;
      }
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
        return;
      }
      this.$refs.hhh[this.centerIndex].huifuCenter();
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
#app {
  .bg{
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top:0;
    background-image: url('./common/img/bg.jpg');
    background-size: cover;
    filter: blur(10px);
  }
  width: 100%;
  height: 100%;
  /*background-color: darkgrey;*/

  overflow: hidden;
  position: fixed;
  top:0;
  left:0;
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
    top: 90%;
  }
}
</style>
