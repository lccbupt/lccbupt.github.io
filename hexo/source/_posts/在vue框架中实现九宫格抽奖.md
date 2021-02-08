---
title: 在vue框架中实现九宫格抽奖
date: 2020-05-22 09:57:25
tags: vue
categories: Vue
---
一、功能实现
在近期开发的活动页面中需要实现九宫格转盘抽奖的功能，使用vue框架，这里记录一下具体功能实现的方法。
二、具体实现方法
- 抽奖相关信息如奖品信息，抽奖次数等由服务端接口获取
- 前端展示九宫格奖品信息，并实现点击抽奖后九宫格的顺时针点亮转动
- 转动效果实现是每次点亮对应li标签的背景颜色
<!--more-->
{% asset_img draw.png 实现效果图 %}
三、具体代码实现
1.vue的html模板
该部分实现的方法略微有些繁琐，具体代码如下：

        <template>
          <div class="draw-cnt">
              <ul>
                <li :class="activeIndex === 0 ? 'active' : ''"></li>
                <li :class="activeIndex === 1 ? 'active' : ''"></li>
                <li :class="activeIndex === 2 ? 'active' : ''"></li>
                <li :class="activeIndex === 7 ? 'active' : ''"></li>
                <li class="drawbtn" @click="startDraw"></li>
                <li :class="activeIndex === 3? 'active' : ''"></li>
                <li :class="activeIndex === 6 ? 'active' : ''"></li>
                <li :class="activeIndex === 5 ? 'active' : ''"></li>
                <li :class="activeIndex === 4 ? 'active' : ''"></li>
              </ul>
          </div>
        </template>

2.vue的数据展示和js代码
首先需要定义几个控制九宫格转动的变量如下：

        data () {
            return {
              prizelist: [], // 奖品列表
              prizeIds: [],
              activeIndex: -1, // 当前active位置
              round: 8, // 转动周期
              times: 0, // 转动圈数
              speed: 200, // 转动速度
              clicked: false,//是否点击按钮正在抽奖转动中
              timer: null, //定时器
              prizeid: 0, // 中奖id
            }
        }

具体实现逻辑：
1.点击抽奖后请求接口，确定中奖id：prizeid
2.开始移动当前点亮li标签的位置：前进一格，并记录前进次数
3.当前进次数大于规定次数，并且当前点亮的li标签activeIndex等于中奖id对应的位置，则停止转动，展示提示信息
4.否则继续第2步骤方法，并改变转动速度

        startDraw () {
          if (this.clicked) {
            return false
          }
          let that = this
          this.$api.queryDraw().then(data => {
            if (data === 'nodraw') {
              that.prizeid = 7
            } else {
              that.prizeid = that.prizeIds.indexOf(Number(data.prizeid))
              that.prizeinfo = data
              console.log(data.prizeid)
            }
            that.getDrawTimes()
          })
          this.startRoll()
        },
        startRoll () {
          this.times += 1
          this.clicked = true
          this.Rollup()
        },
        oneRound () {
          let index = this.activeIndex
          const count = 8
          index += 1
          if (index > count - 1) {
            index = 0
          }
          this.activeIndex = index
        },
        Rollup () {
          this.oneRound()
          let that = this
          if (this.times > this.round + 12 && this.activeIndex === this.prizeid) {
            this.times = 0
            clearTimeout(this.timer)
            this.speed = 200
            this.clicked = false
            setTimeout(() => {
              that.popResult()
            }, 500)
          } else {
            if (this.times < this.round) {
              this.speed -= 5
            }
            this.timer = setTimeout(this.startRoll, this.speed)
          }
        }