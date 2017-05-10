<template>
  <div id="app" ref="stage">
    <div v-for="(value,index) in imgData" ref="imgs" class="imgCom" :style="imgStyle[index]">
      <figureMy :srcMy="value.imgName" :title="value.title" :desc="value.desc"></figureMy>
    </div>
  </div>
</template>
<script>
import figureMy from './components/figure/figure';
import './common/less/base.less';
import dataOld from '../data.json';

export default {
  name: 'app',
  data() {
    return {
      imgData: [],
      dataOld: dataOld,
      halfStageW: 0,
      halfStageH: 0,
      halfImgW: 0,
      halfImgH: 0,
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
    figureMy
  },
  created: function () {
    this.$nextTick(function(){
      this._init();
//      this.rearrange(this.centerIndex);
    });
  },
  methods: {
    /**
     * 重新排列图片组件
     * @param centerIndex (num) 位于中心的图片的索引
     */
//    rearrange: function (centerIndex) {
//      const len = this.imgData.length;
//      for (let i = 0; i<len; i++){
//        if (i === centerIndex){
//          this.imgStyle[i] = {
//            left : this.centerPos.left + 'px',
//            top : this.centerPos.top + 'px'
//          };
//        } else if (i <= Math.ceil(len/2)) {
//          this.imgStyle[i] = {
//            left : this.getRangeNum(...this.hPosRange.leftSecX) + 'px',
//            top : this.getRangeNum(...this.hPosRange.y) + 'px'
//          };
//        } else {
//          this.imgStyle[i] = {
//            left : this.getRangeNum(...this.hPosRange.rightSecX) + 'px',
//            top : this.getRangeNum(...this.hPosRange.y) + 'px'
//          };
//        }
//      }
//    },
    // 初始化数据
    _init: function() {
      const stageDOM = this.$refs.stage;
      const stageW = parseInt(stageDOM.scrollWidth);
      const stageH = parseInt(stageDOM.scrollHeight);

      this.halfStageW = Math.ceil(stageW/2);
      this.halfStageH = Math.ceil(stageH/2);
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
        this.halfImgW = Math.ceil(ImgW/2);
        this.halfImgH = Math.ceil(ImgH/2);
        // 图片中心位置定位赋值
        this.centerPos.left = this.halfStageW - this.halfImgW;

        this.centerPos.top = this.halfStageH - this.halfImgH;
        // 左右区域取值范围赋值
        this.hPosRange.leftSecX = [-this.halfImgW, this.halfStageW - this.halfImgW * 3];
        this.hPosRange.rightSecX = [this.halfStageW + this.halfImgW, 2 * this.halfStageW - this.halfImgW];
        this.hPosRange.y = [-this.halfImgH, 2 * this.halfStageH - this.halfImgH];
        // 上部分的取值范围
        this.vPosRange.x = [this.halfStageW - 2 * this.halfImgW, this.halfStageW];
        this.vPosRange.topY = [-this.halfImgW, this.halfStageH - 3 * this.halfImgH];
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
            transform : 'rotate(0)'
          };
        } else if (i === Math.ceil(len/2)){
          arr[i] = {
            left : this.getRangeNum(...this.vPosRange.x) + 'px',
            top : this.getRangeNum(...this.vPosRange.topY) + 'px',
            transform : 'rotate('+this.getRangeNum(-30, 30)+'deg)'
          }
        } else if (i < Math.ceil(len/2)) {
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
#app {
  width: 100%;
  height: 100%;
  background-color: darkgrey;
  overflow: hidden;
  position: fixed;
  top:0;
  left:0;
  .imgCom{
    position: absolute;
  }
}
</style>
