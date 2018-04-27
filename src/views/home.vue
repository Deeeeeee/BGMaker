<template>
  <v-container fluid>
    <v-slide-y-transition mode="out-in">
      <v-layout column align-center>
        <canvas id="canvas" :width="canvasWidth" :height="canvasHeight" style="background-color: #000"></canvas>
      </v-layout>
    </v-slide-y-transition>
  </v-container>
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<script>
  export default {
    data () {
      return {
        canvas: null,
        ctx: null, // 花边
        canvasWidth: 1000,
        canvasHeight: 500,
        dataArr: [
          {
            name: '空间',
            count: 30
          },
          {
            name: '是胜多负少',
            count: 20
          },
          {
            name: '空刚刚间',
            count: 1
          },
          {
            name: '空的间',
            count: 2
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: 'git branch -d hotfixes',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          },
          {
            name: '梵蒂冈间',
            count: 3
          }
        ],
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
    },
    mounted () {
//      this.canvas = document.getElementById('canvas')
//      this.ctx = this.canvas.getContext('2d')
//      this.ctx.fillStyle = '#000'
//      this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height)
      this.ready(this.data2, 'canvas')
    },
    methods: {
      ready: function () {
        this.canvas = document.getElementById('canvas')

        this.ctx = this.canvas.getContext('2d')
        this.finImgData = this.ctx.createImageData(this.canvasWidth, this.canvasHeight)
        this.finImgMsg = []

        for (var i = 0; i < this.canvasWidth; i++) {
          this.finImgMsg[i] = []
          for (var j = 0; j < this.canvasHeight; j++) {
            this.finImgMsg[i][j] = 0
          }
        }
        this.cavulateData()
        // log(this.data)
        this.draw()
      },
      /**
       * 计算标签大小
       */
      cavulateData: function () {
        this.dataArr.sort(function (x, y) {
          if (Math.floor(x.count) === Math.floor(y.count)) {
            return 0
          }
          if (Math.floor(x.count) > Math.floor(y.count)) {
            return -1
          } else {
            return 1
          }
        })
        this.dataArr.map((item, index) => {
          if (item.count < 12) {
            item.count = 12
          }
        })
        console.table(this.dataArr)
      },
      /**
       * 开始画
       */
      draw: function () {
        for (var i = 0; i < this.dataArr.length; i++) {
          this.drawWord(this.dataArr[i].name, this.dataArr[i].count)
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
        let wordImgData = this.ctx.getImageData(0, 0, w, size + 10)
        wordImgData = this.randomRotateImgData(wordImgData)
        this.ctx.clearRect(0, 0, this.canvasWidth, this.canvasHeight)
        // 初始化查找点
        let centerPoint = this.getCenterPoint()
        let i = 0
//        return
        while (i < 1000) {
          if (centerPoint.isFullRound()) {
            centerPoint.clearRound()
          }
          var pos = centerPoint.getCenterPos(wordImgData.width, wordImgData.height)
          pos.x = this.canvasWidth / 2 + pos.x
          pos.y = this.canvasHeight / 2 + pos.y

          if (this.isAbleDraw(wordImgData, pos.x, pos.y)) {
            for (let i = 0; i < wordImgData.width; i++) {
              for (var j = 0; j < wordImgData.height; j++) {
                var point = this.getXY(wordImgData, i, j)
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
        var w = wordImg.width
        var h = wordImg.height

        for (var i = 0; i < w; i++) {
          for (var j = 0; j < h; j++) {
            var wordPoint = this.getXY(wordImg, i, j)
            // 检测文字图片上该点是否有痕迹，不全为白为有痕迹
            if (wordPoint[3] !== 0) {
              var finx = x + i - 1
              var finy = y + j - 1
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
//        var newImageData = this.ctx.createImageData(imgData.height, imgData.width)
//        if (this.random(9) > 6) {
//          for (var i = 0; i < imgData.height; i++) {
//            for (var j = 0; j < imgData.width; j++) {
//              var point = this.getXY(imgData, j, i)
//              this.setXY(newImageData, imgData.height - i - 1, j, point)
//            }
//          }
//          imgData = newImageData
//        }

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
              var chooseCount = this.round === 1 ? 1 : this.round * 2 + (this.round - 2) * 2 // 总共可以选择的点
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
              var shift = 0.5 // 偏移率
              var shiftw = _this.random(1) ? _this.random(w * shift) : -_this.random(w * shift)
              var shifth = _this.random(1) ? _this.random(h * shift) : -_this.random(h * shift)
              var pos = {
                x: 0,
                y: 0
              }

              if (this.nowChoose === null) {
                return false
              }

              if (this.round !== 1) {
                var quadrant = Math.floor((this.nowChoose) / (this.round - 1)) // 第几象限
                var distance = (this.nowChoose + 1) % this.round// 象限的偏移

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
              var chooseCount = this.round === 1 ? 1 : this.round * 2 + (this.round - 2) * 2 // 总共可以选择的点
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
      randomColor () {
        return '#' + ('00000' + (Math.random() * 0x1000000 << 0).toString(16)).slice(-6)
      },

      random (num) {
        return Math.floor(Math.random() * (num + 1))
      },
      /**
       * imgData根据坐标获取
       */
      getXY (imgData, x, y) {
        var res = []
        var w = imgData.width

        var pos = (y * w + x) * 4

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
        var w = imgData.width

        var pos = (y * w + x) * 4

        imgData.data[pos] = res[0]
        imgData.data[pos + 1] = res[1]
        imgData.data[pos + 2] = res[2]
        imgData.data[pos + 3] = res[3]
      },

      inArray (son, arr) {
        for (var i = 0; i < arr.length; i++) {
          if (arr[i] === son) {
            return true
          }
        }
        return false
      },

      changeImg () {
//        var containerDom = document.getElementById('img')
//        containerDom.innerHTML = ''

//        drawImg.ready(data2, 'img')
      }
    }
  }
</script>

<style scoped>

</style>
