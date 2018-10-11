<template>
  <div class="ev-index">
     <!-- 顶部按钮 -->
      <div class="en-top">
        <Button  v-for="(list,index) in btn"  @click="change(index)" :class="{red:index == changeRed}" :key="index">{{list.a}}</Button>
        <!-- <Button  v-for="(list,index) in btn" :class="{red:index == 1}" :key="index">{{list.a}}</Button> -->
      </div>
      <!-- 图 -->
      <div class="en-img">
        <p class="door-ev">室内平均{{btnName}} <span class="indoor-ev">25.5</span> ℃<span class="outdoor-ev">室外平均{{btnName}} </span><span class="indoor-ev">28</span> ℃</p>
        <!-- 标尺 -->
        <div class="legend">
            <div class="line"> <span class="ev-line"> ℃</span> </div>
            <ul>
              <li>-10</li>
              <li>10</li>
              <li>20</li>
              <li>25</li>
              <li>30</li>
              <li>40</li> 
            </ul>       
          </div> 

        <div class="equipment-view">
          <div class="floor" v-for="(item1, index1) in myData" :key="index1">
            <p style="margin:40px 30px 14px 0">
                <span @click="showFloor(item1)" class="floor-num">第{{item1.floor}}层</span>
              平均{{btnName}}&nbsp;&nbsp;&nbsp;&nbsp;{{item1.averageTemperature}}℃</p>
            <ul>
              <!-- 展示温度 -->
              <template v-if="btnName=='温度'">
                <router-link to="heal">
                  <li v-for="(item2, index2) in item1.roomList" :key="index2" :style="`background-color: ${tempToColor(item2.temp)}`">
                    <span>{{item2.temp}}</span>
                    <hover-div :item2="item2.temp" :btnName="btnName"></hover-div>
                  </li>
                </router-link>
              </template>
              <!-- 展示湿度 -->
              <template  v-if="btnName=='CO2'">
                <li v-for="(item2, index2) in item1.roomList" :key="index2" :style="`background-color: ${tempToColor(item2.humidity)}`">
                  <span>{{item2.humidity}}</span>
                  <hover-div :item2="item2.humidity" :btnName="btnName"></hover-div>
                </li>
              </template>
            </ul>
          </div> 
        </div>
      </div>  
      <!-- 底部的两个图 -->
      <div class="en-table">
        <temperature-bar></temperature-bar>
        <temperature-table></temperature-table>
      </div>
  </div>
</template>

<script>
import axios from 'axios'
import hoverDiv from './HoverDiv.vue'
import temperatureBar from './TemperatureBar.vue'
import temperatureTable from './TemperatureTable.vue'
export default {
  components: {
    hoverDiv,
    temperatureBar,
    temperatureTable
 },
  mixins: [],
  props:[],
  data () {
    return {
      btn:[{"a":"健康指数" },{"a":"温度"},{"a":"CO2"},{"a":"PM2.5"},{"a":"情绪"},{"a":"照度"},{"a":"人流量"}],
      btnName:"温度",
      changeRed:1,
      showTemp: false,
      tempAndColorMap: {
        temp_10: '7787FE', // 下划线代表负值
        temp10: '5cace5',
        temp20: '95e078',
        temp25: 'f3e650',
        temp30: 'f7c140',
        temp40: 'fb6f69'
      },
      myData: [
        {
          floor: 2, // 楼层
          averageTemperature: 12.5, // 平均温度
          averageHumidity: 12.5, // 平均湿度
          roomList: [
            {
              temp: 25.6, // 房间温度
              humidity: 25 // 房间湿度
            },
            {
              temp: 5.6, // 房间温度
              humidity: 14.4 // 房间湿度
            },
            {
              temp: 29.6, // 房间温度
              humidity: 32.4 // 房间湿度
            },
            {
              temp: 24.6, // 房间温度
              humidity: 22.4 // 房间湿度
            },
            {
              temp: 35.6, // 房间温度
              humidity: 19.4 // 房间湿度
            },
            {
              temp: 30.6, // 房间温度
              humidity: 12.4 // 房间湿度
            },
            {
              temp: 30.6, // 房间温度
              humidity: 1.4 // 房间湿度
            },
            {
              temp: 30.6, // 房间温度
              humidity: 2.4 // 房间湿度
            },
            {
              temp: 30.6, // 房间温度
              humidity: 1.4 // 房间湿度
            }
          ]
        },
        {
          floor: 1, // 楼层
          averageTemperature: 12.5, // 平均温度
          averageHumidity: 12.5, // 平均湿度
          roomList: [
            {
              temp: 25.6, // 房间温度
              humidity: 12.4 // 房间湿度
            },
            {
              temp: 5.6, // 房间温度
              humidity: 12.4 // 房间湿度
            },
            {
              temp: 29.6, // 房间温度
              humidity: 12.4 // 房间湿度
            },
            {
              temp: 24.6, // 房间温度
              humidity: 12.4 // 房间湿度
            },
            {
              temp: 35.6, // 房间温度
              humidity: 12.4 // 房间湿度
            },
            {
              temp: 30.6, // 房间温度
              humidity: 12.4 // 房间湿度
            },
            {
              temp: 30.6, // 房间温度
              humidity: 12.4 // 房间湿度
            },
            {
              temp: 30.6, // 房间温度
              humidity: 12.4 // 房间湿度
            },
            {
              temp: 30.6, // 房间温度
              humidity: 12.4 // 房间湿度
            }
          ]
        }
      ]
    }
  },
  computed: {
  },
  mounted () {
  },
  methods: {
    /**
     * 计算方法：【重点】一定要取准确色值，这里使用了qq取色，不准。
     * -10  7787FE (qq取色，不准）  ----> 对应的10进制： 7833598
        10  5cace5（qq取色，不准）
        20  95e078（qq取色，不准）
        25  f3e650（qq取色，不准）
        30  f7c140（qq取色，不准）
        40  fb6f69 (qq取色，不准） ------> 对应的10进制： 16478057
        进制转换工具： https://tool.lu/hexconvert/
        *** 再次强调：必须知道-10和40的准确值
        系数： (16478057 - 7833598)/50 = 172889.18   // 每加1代表色值加172889.18
        公式：色值对应的10进制 num = 步数 * 系数 + 底数， 最后parseInt(num).toString(16);
        则25度色值  (25 - (-10)) * 172889.18 + 7833598 = 13884719  ===>  d3dd2f（根据7787FE和fb6f69计算的准确值）
     */
     tempToColor (temperature) {
      let temp = parseFloat(temperature) // 转为浮点型
      let minArr = [] // 切割后的16进制，eg: '7787FE'  => [77, 87, FE]
      let maxArr = [] // 切割后的16进制，eg: '7787FE'  => [5c, ac, e5]
      let length = 0 // 温度区间, eg：-10~20 length 为 20
      let minTemp = 0 // 区间最小温度, eg: -10
      if (temp >= -10 && temp < 10) {
        minArr = this.tempAndColorMap.temp_10.match(/[\d\w]{2}/g)
        maxArr = this.tempAndColorMap.temp10.match(/[\d\w]{2}/g)
        length = 20
        minTemp = -10
      } else if (temp >= 10 && temp < 20) {
        minArr = this.tempAndColorMap.temp10.match(/[\d\w]{2}/g)
        maxArr = this.tempAndColorMap.temp20.match(/[\d\w]{2}/g)
        length = 10
        minTemp = 10
      } else if (temp >= 20 && temp < 25) {
        minArr = this.tempAndColorMap.temp20.match(/[\d\w]{2}/g)
        maxArr = this.tempAndColorMap.temp25.match(/[\d\w]{2}/g)
        length = 5
        minTemp = 20
      } else if (temp >= 25 && temp < 30) {
        minArr = this.tempAndColorMap.temp25.match(/[\d\w]{2}/g)
        maxArr = this.tempAndColorMap.temp30.match(/[\d\w]{2}/g)
        length = 5
        minTemp = 25
      } else if (temp >= 30 && temp <= 40) {
        minArr = this.tempAndColorMap.temp30.match(/[\d\w]{2}/g)
        maxArr = this.tempAndColorMap.temp40.match(/[\d\w]{2}/g)
        length = 10
        minTemp = 30
      }
      let minNum = minArr.map(i => parseInt(i, 16)) // 每个区间最小温度对应色值（10进制) eg: [92, 172, 229]
      let maxNum = maxArr.map(i => parseInt(i, 16)) // 每个区间最大温度对应色值（10进制) eg: [92, 172, 229]
      let coef = [] // 系数
      let num = [] //  当前温度对应色值(10进制)
      for (let i = 0; i < 3; i++) {
        // 计算系数
        coef[i] = (maxNum[i] - minNum[i]) / length
        // 计算温度色值（10进制）
        num[i] = (temp - minTemp) * parseFloat(coef[i]) + minNum[i]
      }
      // 返回温度色值数组
      return `rgb(${num.toString()})`
      // return num.reduce((i, j) => `${i},${j}`)
    },
    //点击的按钮名称
    change(index){
      this.changeRed = index;
      this.btnName = this.btn[index].a;
    },
    // 传参数 第几层
    showFloor(item1){
      console.log(item1)
      this.$router.push({path:'analys',query:{floorId:item1.floor}})
    }
  }
}
</script>

<style scoped lang="less" >
.ev-index{
  background: #fff;
  padding: 20px;
  box-shadow: 0 1px 4px 0 rgba(0,0,0,0.05);
  border-radius: 2px;
  min-width: 1819px;
  .en-top{
    text-align: center;
    button{
      width: 96px;
      height: 30px;
      text-align: center;
      display: inline-block;
    }
    .red{
      background:#57A4F7;
      color:#fff;
    }
  }
  .en-img{
    .door-ev{
      width:360px;
      height: 42px;
      line-height: 42px;
      background: #F4F5F8;
      border-radius: 1px; 
      padding-left: 20px;
      margin:40px 0;
      .indoor-ev{
        color: #57A4F7;
        padding-left:14px;
      }
      .outdoor-ev{
        padding-left: 40px;
      }
    }
       .legend {
        float: right;
        margin-top: -70px;
        margin-right: 200px;
        .line {
            width: 300px;
            height: 10px;
            background: linear-gradient(to right, #7787FE, #5cace5, #95e078, #f3e650 62.5%, #f7c140 75%, #fb6f69);
            border-radius: 3px;
            position: relative;
            .ev-line{
              position: absolute;
              left: 308px;
              top:-3px;
            }
        }
        ul {
          li {
            float: left;
            &:not(:first-child) {
              padding-left: 50px;
            }
            &:nth-child(4), &:nth-child(5) {
              padding-left: 33px;
            }
          }
        }
      }
    .equipment-view {
      .floor  {
        .floor-num{
          padding: 6px 19px;
          font-size:14px;
          font-weight:bold;
          margin-right:30px;
          color: #4A9EF9;
          text-align: center;
          background: #fff;
          cursor: pointer;
          border: 1px solid #4A9EF9;
          border-radius: 1px;
        }
        ul {
          li {
            display: inline-block;
            width: 164px;
            height: 64px;
            position: relative;
            margin-right: 4px;
            border-radius: 2px;
            &:hover .hoverdiv{
              display: block;
            }
            span {
              position: absolute;
              // padding: 7px 13px;
              width:56px;
              height:28px;
              line-height: 28px;
              text-align: center;
              display:inline-block;
              background: #fff;
              right: 10px;
              bottom: 10px;
              border-radius: 1.5px;
              opacity: 0.4;
            }
          }
        }
      }
    }
  }
  .en-table{
    display:flex;
    margin-top: 60px;
    .en-good{
     padding-right: 20px;
    }
    .en-bad{
      
    }
  }
}

</style>
