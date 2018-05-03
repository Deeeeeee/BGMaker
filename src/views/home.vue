<template>
  <v-container fluid>
    <v-slide-y-transition mode="out-in">
      <v-layout column align-center>
        <v-navigation-drawer
          persistent
          :mini-letiant="miniVariant"
          v-model="drawer"
          enable-resize-watcher
          fixed
          right
          app
          width="400"
        >
          <v-form ref="form" class="px-2 py-4" lazy-validation>
            <v-text-field label="宽度" v-model="canvasWidth" type="number" required></v-text-field>
            <v-text-field label="高度" v-model="canvasHeight" type="number" required></v-text-field>
            <v-slider label="缩放" :min="1" :max="10" v-model="zoom"></v-slider>
            <v-switch label="方向随机" v-model="isRandomDirection" color="success" value="success" hide-details></v-switch>
            <!--<v-tabs v-model="active" color="cyan" dark slider-color="yellow">-->
            <!--<v-tab ripple>手动导入</v-tab>-->
            <!--<v-tab ripple>Json导入</v-tab>-->
            <!--<v-tab-item>-->
            <!--<v-text-field label="文本" v-model="inputData.name" required></v-text-field>-->
            <!--<v-text-field label="字号" v-model="inputData.fontSize" required></v-text-field>-->
            <!--<v-btn @click="add" color="info">添加</v-btn>-->
            <!--<v-btn @click="backout" color="warning">撤销</v-btn>-->
            <!--</v-tab-item>-->
            <!--<v-tab-item>-->
            <!--<v-text-field :multi-line="true" label="Json" v-model="dataArrString" required></v-text-field>-->
            <!--<v-btn @click="multiCreate" color="info" class="w-100" :disabled="!dataArrString">批量生成</v-btn>-->
            <!--</v-tab-item>-->
            <!--</v-tabs>-->
            <v-text-field class="textarea" :multi-line="true" label="Json" v-model="dataArrString"
                          :placeholder="JSON.stringify(this.dataArr)"
                          required></v-text-field>
            <v-btn @click="multiCreate" color="info" class="w-100" :disabled="!dataArrString">批量生成</v-btn>

            <div class="btn-group">
              <v-btn @click="create" color="info">重新渲染</v-btn>
              <v-btn @click="clean" color="warning">清空画布</v-btn>
              <v-btn @click="downloadFile('bg.jpeg')" color="success">保存图片</v-btn>
            </div>
          </v-form>
        </v-navigation-drawer>
        <canvas id="canvas" :width="canvasWidth" :height="canvasHeight" :style="{
          backgroundColor: '#000',
          transform: 'scale('+ zoom / 10 + ')',
          }"></canvas>
        <v-btn class="btn-setting" @click.stop="drawer=!drawer" fab dark small color="blue">
          <v-icon dark>settings</v-icon>
        </v-btn>
        <v-snackbar
          :top="true"
          :timeout="2000"
          color="error"
          v-model="snackbar"
        >Json格式有误，请参考示例
        </v-snackbar>
      </v-layout>
    </v-slide-y-transition>
  </v-container>
</template>

<script>
  export default {
    data () {
      return {
        inputData: {
          name: '',
          fontSize: 12
        },
        active: '0',
        drawer: true,
        clipped: false,
        miniVariant: false,
        zoom: 5,
        canvas: null,
        ctx: null,
        canvasWidth: 1920,
        canvasHeight: 1080,
        ratio: 1,
        snackbar: false,
        isRandomDirection: false,
        dataArr: [
          {
            'name': '下班打卡',
            'fontSize': 40
          }
        ],
        dataArrString: '',
        finImgData: null, // 最终图片
        finImgMsg: null, // 存放是否已写信息
        colorArr: [ // 颜色选择
          '#FFFFCC',
          '#CCFFFF',
          '#FFCCCC',
          '#FF9999',
          '#FFFF99',
          '#CCCCFF',
          '#CCFF99',
          '#99CCFF',
          '#FF9900',
          '#99CCFF',
          '#CCFFCC',
          '#9999FF',
          '#FF33CC',
          '#FF3399',
          '#FFFF99',
          '#CCFF66',
          '#CCFF00',
          '#CCFFFF',
          '#FF6666',
          '#FFFFFF',
          '#CCFFCC',
          '#FF99CC'
        ]
      }
    },
    props: [],
    computed: {},
    components: {},
    watch: {},
    created () {
//      this.ratio = window.devicePixelRatio || 1
//      this.canvasWidth = this.canvasWidth * this.ratio
//      this.canvasHeight = this.canvasHeight * this.ratio
    },
    mounted () {
      this.ready(this.dataArr)
    },
    methods: {
      ready: function () {
        this.canvas = document.getElementById('canvas')
        this.ctx = this.canvas.getContext('2d')
        this.finImgData = this.ctx.createImageData(this.canvasWidth, this.canvasHeight)
        this.finImgMsg = []
        for (let i = 0; i < this.canvasWidth; i++) {
          this.finImgMsg[i] = []
          for (let j = 0; j < this.canvasHeight; j++) {
            this.finImgMsg[i][j] = 0
          }
        }
        this.setFontSize()
        this.draw()
      },
      /**
       * 计算标签大小
       */
      setFontSize: function () {
        this.dataArr.sort(function (x, y) {
          if (Math.floor(x.fontSize) === Math.floor(y.fontSize)) {
            return 0
          }
          if (Math.floor(x.fontSize) > Math.floor(y.fontSize)) {
            return -1
          } else {
            return 1
          }
        })
        this.dataArr.map((item, index) => {
//          item.fontSize = item.fontSize * this.ratio
          if (item.fontSize < 12) {
            item.fontSize = 12
          }
        })
      },
      /**
       * 开始画
       */
      draw: function () {
        for (let i = 0; i < this.dataArr.length; i++) {
          this.drawWord(this.dataArr[i].name, this.dataArr[i].fontSize)
        }
        this.ctx.putImageData(this.finImgData, 0, 0)
      },
      /**
       * 单一一个标签画
       */
      drawWord: function (word, size) {
        let fillStyle = this.colorArr[this.random(this.colorArr.length - 1)]
//        this.ctx.fillStyle = this.randomColor()
        this.ctx.fillStyle = fillStyle
        this.ctx.font = '900 ' + size + 'px 微软雅黑'
        let w = this.ctx.measureText(word).width
        this.ctx.textBaseline = 'top'
        this.ctx.fillText(word, 0, 0)
        let wordImgData = this.ctx.getImageData(0, 0, w + 30, size + 30)
        if (this.isRandomDirection) {
          wordImgData = this.randomRotateImgData(wordImgData)
        }
        this.ctx.clearRect(0, 0, this.canvasWidth, this.canvasHeight)
        // 初始化查找点
        let centerPoint = this.getCenterPoint()
        let i = 0
//        return
        while (i < 1000) {
          if (centerPoint.isFullRound()) {
            centerPoint.clearRound()
          }
          let pos = centerPoint.getCenterPos(wordImgData.width, wordImgData.height)
          pos.x = this.canvasWidth / 2 + pos.x
          pos.y = this.canvasHeight / 2 + pos.y

          if (this.isAbleDraw(wordImgData, pos.x, pos.y)) {
            for (let i = 0; i < wordImgData.width; i++) {
              for (let j = 0; j < wordImgData.height; j++) {
                let point = this.getXY(wordImgData, i, j)
                if (point[3] !== 0) {
                  this.setXY(this.finImgData, pos.x + i, pos.y + j, point)
                  this.finImgMsg[pos.x + i - 1][pos.y + j - 1] = 1
                }
              }
            }
            break
          }
          i++
          centerPoint = this.getCenterPoint(centerPoint)
        }
      },

      /**
       * 是否可以画
       */
      isAbleDraw: function (wordImg, x, y) {
        let w = wordImg.width
        let h = wordImg.height

        for (let i = 0; i < w; i++) {
          for (let j = 0; j < h; j++) {
            let wordPoint = this.getXY(wordImg, i, j)
            // 检测文字图片上该点是否有痕迹，不全为白为有痕迹
            if (wordPoint[3] !== 0) {
              let finx = x + i - 1
              let finy = y + j - 1
              if (finx < 0 || finx >= this.canvasWidth || finy < 0 || finy >= this.canvasHeight) {
                return false
              }
              if (this.finImgMsg[finx][finy] === 1) {
                return false
              }
            }
          }
        }
        return true
      },
      /**
       * 随机旋转
       */
      randomRotateImgData: function (imgData) {
        let newImageData = this.ctx.createImageData(imgData.height, imgData.width)
        if (this.random(9) > 6) {
          for (let i = 0; i < imgData.height; i++) {
            for (let j = 0; j < imgData.width; j++) {
              let point = this.getXY(imgData, j, i)
              this.setXY(newImageData, imgData.height - i - 1, j, point)
            }
          }
          imgData = newImageData
        }

        return imgData
      },

      /**
       * 用于标签的位置选择
       */
      getCenterPoint: function (centerPoint) {
        let _this = this
        let c = {}
        // 没有传入centerPoint,默认初始化
        if (typeof centerPoint !== 'object') {
          // centerPoint对象，用于存储以往已经选择的点的信息
          c = {
            round: 1, // 第几圈
            choose: [], // 已选择的点
            nowChoose: null,
            revert: 0,
            /**
             * 随机选择点
             */
            randPoint: function () {
              let chooseCount = this.round === 1 ? 1 : this.round * 2 + (this.round - 2) * 2 // 总共可以选择的点
              // 所有情况已经遍历了，增加一环 ,重置
              if (this.choose.length === chooseCount) {
                this.round++
                this.choose = []
                this.revert = 0
                return this.randPoint()
              }

              while (true) {
                this.nowChoose = _this.random(chooseCount - 1)
                if (!_this.inArray(this.nowChoose, this.choose)) {
                  this.choose.push(this.nowChoose)
                  break
                }
              }

              return this.nowChoose
            },
            getCenterPos: function (w, h) {
              let shift = 0.5 // 偏移率
              let shiftw = _this.random(1) ? _this.random(w * shift) : -_this.random(w * shift)
              let shifth = _this.random(1) ? _this.random(h * shift) : -_this.random(h * shift)
              let pos = {
                x: 0,
                y: 0
              }

              if (this.nowChoose === null) {
                return false
              }

              if (this.round !== 1) {
                let quadrant = Math.floor((this.nowChoose) / (this.round - 1)) // 第几象限
                let distance = (this.nowChoose + 1) % this.round// 象限的偏移
                // log(quadrant)
                switch (quadrant) {
                  case 0 :
                    pos.x = w / 2 * distance
                    pos.y = h / 2 * (this.round - distance)
                    break
                  case 1 :
                    pos.x = w / 2 * (this.round - distance)
                    pos.y = h / 2 * (-distance)
                    break
                  case 2 :
                    pos.x = w / 2 * (-distance)
                    pos.y = h / 2 * -(this.round - distance)
                    break
                  case 3 :
                    pos.x = w / 2 * -(this.round - distance)
                    pos.y = h / 2 * distance
                    break
                }
              }
              pos.x += shiftw
              pos.y += shifth
              pos.x = Math.floor(pos.x - w / 2)
              pos.y = Math.floor(pos.y - h / 2)
              return pos
            },
            isFullRound: function () {
              if (this.revert) return false
              let chooseCount = this.round === 1 ? 1 : this.round * 2 + (this.round - 2) * 2 // 总共可以选择的点
              return this.choose.length === chooseCount
            },
            clearRound: function () {
              this.choose = []
              this.revert = 1
            }
          }
        } else {
          c = centerPoint
        }
        c.randPoint()
        return c
      },
      random (num) {
        return Math.floor(Math.random() * (num + 1))
      },
      /**
       * imgData根据坐标获取
       */
      getXY (imgData, x, y) {
        let res = []
        let w = imgData.width

        let pos = (y * w + x) * 4

        res[0] = imgData.data[pos]
        res[1] = imgData.data[pos + 1]
        res[2] = imgData.data[pos + 2]
        res[3] = imgData.data[pos + 3]
        return res
      },
      /**
       * imgData根据坐标修改
       */
      setXY (imgData, x, y, res) {
        let w = imgData.width

        let pos = (y * w + x) * 4

        imgData.data[pos] = res[0]
        imgData.data[pos + 1] = res[1]
        imgData.data[pos + 2] = res[2]
        imgData.data[pos + 3] = res[3]
      },

      inArray (son, arr) {
        for (let i = 0; i < arr.length; i++) {
          if (arr[i] === son) {
            return true
          }
        }
        return false
      },
      base64Img2Blob (code) {
        let parts = code.split(';base64,')
        let contentType = parts[0].split(':')[1]
        let raw = window.atob(parts[1])
        let rawLength = raw.length
        let uInt8Array = new Uint8Array(rawLength)
        for (let i = 0; i < rawLength; ++i) {
          uInt8Array[i] = raw.charCodeAt(i)
        }
        return new Blob([uInt8Array], {type: contentType})
      },
      downloadFile (fileName) {
        let aLink = document.createElement('a')
        let blob = this.base64Img2Blob(this.canvas.toDataURL('image/jpeg')) // new Blob([content])
        console.log(1)
        let evt = document.createEvent('MouseEvents')
        evt.initEvent('click', false, false)// initEvent 不加后两个参数在FF下会报错
        aLink.download = fileName
        aLink.href = URL.createObjectURL(blob)
        aLink.dispatchEvent(evt)
      },
      /**
       * 添加数据
       */
      add () {
        this.dataArr.push(JSON.parse(JSON.stringify(this.inputData)))
        this.inputData.name = ''
        this.ready(this.dataArr)
      },
      /**
       * 添加数据
       */
      backout () {
        this.dataArr.splice(this.dataArr.length - 1, 1)
        this.ready(this.dataArr)
      },
      /**
       * 生成背景图
       */
      create () {
        this.ready(this.dataArr)
      },
      /**
       * 清空
       */
      clean () {
        this.dataArr = []
        this.ready(this.dataArr)
      },
      multiCreate () {
        let flag = true
        try {
          this.dataArr = JSON.parse(this.dataArrString)
        } catch (err) {
          flag = false
          this.snackbar = true
        }
        flag && this.ready(this.dataArr)
      }

    }
  }
</script>

<style scoped>
  .w-100 {
    width: 100%;
  }

  .btn-setting {
    position: fixed;
    bottom: 5px;
    right: 20px;
    z-index: 999;
  }

  .btn-group {
    position: absolute;
    bottom: 0;
  }

  .textarea {
    height: 200px;
  }
</style>
