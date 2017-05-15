
<template>
  <div style="position: relative;" @mousemove.prevent @selectstart.prevent>
    <div>
      <input type="text" class="thisPickInput" v-model="modelDate"  @click="showDatePickerBox=!showDatePickerBox" :class="modelClass" readonly="true"  :placeholder="placeholder?placeholder:'请选择日期'"><br>
      <div @click.stop class="DatePickerBox DatePickerBoxPosition" v-show="showDatePickerBox">
        <div id="app" class="DatePicker">
          <div class="DatePickerTop">
            <span @click="year--" @dblclick.prevent class="iconfont control">&#xe697;</span><span class="showData" @dblclick.prevent @click="choseYear=year;showChoseMonth=showChoseDate=false;showChoseYear=true;">{{year}}</span><span @click="year++" class="iconfont control">&#xe6a7;</span>
            <span @click="month--" @dblclick.prevent class="iconfont control">&#xe697;</span><span  class="showData" @dblclick.prevent @click="showChoseYear=showChoseDate=false;showChoseMonth=true;">{{month+1}}月</span><span @click="month++" class="iconfont control">&#xe6a7;</span>
          </div>
          <!--今天是{{year}}年{{month+1}}月{{day}}日星期{{week}}有{{monthDayNum}}天1号是星期{{firstWeek}}表格第一天的时间戳是{{firstStamp}}-->
          <div class="DatePickerWeek" v-show="showChoseDate">
            <span v-for="item in 7" class="dayUnit">{{item|toWeek}}</span>
          </div>
          <div class="DatePickerData" v-show="showChoseDate">
        <span v-for="item in dateList" @click="getDate(item)" class="dayUnit" :class="{pickerDay:new Date(item).toDateString() === new Date(modeStamp).toDateString(),noNowMonth:new Date(item).getMonth() !=month}">
            {{item|toDay}}
            <a class="toDay" v-if="new Date(item).toDateString() === new Date().toDateString()">今</a>
        </span>
          </div>
          <!--弹出月份选择-->
          <div class="DatePickerMonth" v-show="showChoseMonth">
            <div  v-for="item in 12" class="monthUnit">
          <span @click="month=item-1;showChoseYear=showChoseMonth=false;showChoseDate=true;" class="dayUnit" >
            {{item}}
            <a class="toDay" v-if="item === new Date().getMonth()+1">今</a>
        </span>
            </div>
          </div>
          <!--弹出年份选择-->
          <div class="DatePickerMonth" v-show="showChoseYear">
            <span @click="choseYear-=12" class="iconfont choseYear" style="left: -7px">&#xe697;</span>
            <span @click="choseYear+=12" class="iconfont choseYear" style="right: -7px">&#xe6a7;</span>
            <div  v-for="item in 12" class="monthUnit">
          <span @click="year=(choseYear+item-5);showChoseYear=showChoseMonth=false;showChoseDate=true;" class="dayUnit" >
            {{choseYear+item-5}}
            <a class="toDay" v-if="(choseYear+item-6) === new Date().getFullYear()">今</a>
        </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

</template>

<script>
    export default {
        props: ['value','modelClass','placeholder'],
        data () {
            return {
                'modelDate':this.value,
                'modeStamp':'',
                'showDatePickerBox':false,
                'today':new Date().getDate(),//当前日
                'year':new Date().getFullYear(), //当前年份
                'month':new Date().getMonth(),//当前月份
                'week':new Date().getDay(),//当前是星期几
                'day':new Date().getDate(),//当前日
                'monthDayNum':new Date(new Date().getFullYear(),new Date().getMonth()+1,0).getDate(),//当前月份天数
                'firstWeek':1,//当前月份一号是星期几
                'firstStamp':1,//当前表格第一格的时间戳
                'firstDayStamp':1,//当前1号时间戳
                'dayStamp':24*60*60*1000 ,//一天的时间戳
                'choseYear':new Date().getFullYear(), //弹出年份选择
                'showChoseDate':true,
                'showChoseMonth':false,
                'showChoseYear':false,
            }
        },
        methods:{
            changeTime:function(){
                if(this.month<0){
                    this.month=11
                }else if(this.month>11){
                    this.month=0
                }
                if(this.year<1000){
                    this.year=1000
                }
                this.getFirstStamp()
            },
            filterDataNum:function (val) {
                return val<10?'0'+val:val
            },
            getFirstStamp:function(){  //获取表格第一天的时间戳
                var timeDate=this.year+'-'+(this.month+1)+'-1 12:12:12'  //当月1号的日期格式
                this.firstDayStamp = Date.parse(new Date(timeDate)); //指定日期的时间戳
                this.firstWeek=new Date(this.year,this.month,1).getDay();//当时间改变时获取改变时间后的第一天的星期
                this.firstStamp=this.firstDayStamp-(this.firstWeek==0?6:this.firstWeek-1)*this.dayStamp;//当表格第一格的时间戳
//                console.log(this.firstWeek)
//               console.log(this.firstStamp)
            },
            getDate:function (data) {
                this.modelDate=this.filterDataNum(this.formatDateYear(data))+'.'+this.filterDataNum((this.formatDateMonth(data)+1))+'.'+this.filterDataNum(this.formatDateDate(data));
                this.modeStamp=data
                this.$emit('input', this.modelDate)
                this.showDatePickerBox=false
            },  //获取选中的是日期
            getPickerMonth:function (month) {

            },
            formatDateYear:function(val) {return new Date(val).getFullYear()},
            formatDateMonth:function(val) {return new Date(val).getMonth()},
            formatDateDate:function(val) {return new Date(val).getDate()},
            formatDateHour:function(val) {return new Date(val).getHours()},
            formatDateMinutes:function(val) {return new Date(val).getMinutes()},
            formatDateSeconds:function(val) {return new Date(val).getSeconds()},
            getValue:function () {
                this.modelDate=this.value
                if(this.modelDate==''||this.modelDate==undefined){
                    this.today=new Date().getDate()//当前日
                    this.year=new Date().getFullYear() //当前年份
                    this.month=new Date().getMonth()//当前月份
                    this.week=new Date().getDay()//当前是星期几
                    this.day=new Date().getDate()//当前日
                }else{
                    if(this.modelDate.indexOf(".") > 0 ){
                        var arr=this.modelDate.split('.')
                    }else   if(this.modelDate.indexOf("-") > 0 ){
                        var arr=this.modelDate.split('-')
                    }else  if(this.modelDate.indexOf("/") > 0 ){
                        var arr=this.modelDate.split('/')
                    }
                    var getArr=arr[0]+'-'+arr[1]+'-'+arr[2]
                    var stringTime=Date.parse(new Date(getArr))
                    this.modeStamp=stringTime
                    this.year=this.formatDateYear(stringTime) //当前年份
                    this.month=this.formatDateMonth(stringTime)//当前月份
                    this.week=new Date().getDay()//当前是星期几
                    this.day=this.formatDateDate(stringTime)//当前日
                }

            }
        },
        computed:{
            'dateList':function(){ //  表格根据firstStampr的变化 而变化
                var dateList=new Array(42)
                for(var i=0; i<42; i++){
                    dateList[i]=(this.firstStamp+i*24*60*60*1000);
                }
                return dateList
            },
        },
        watch:{
            'year':'changeTime',
            'month':'changeTime',    //年月改变时   改变firstStampr
            'value':'getValue'
        },
        mounted:function(){
            if(this.modelDate==''||this.modelDate==undefined){
                this.today=new Date().getDate()//当前日
                this.year=new Date().getFullYear() //当前年份
                this.month=new Date().getMonth()//当前月份
                this.week=new Date().getDay()//当前是星期几
                this.day=new Date().getDate()//当前日
            }else{
                this.getValue()
            }
            this.getFirstStamp()
            document.addEventListener('click', (e) => {
//            console.log(this.$el.contains)
                if (!this.$el.contains(e.target)) this.showDatePickerBox = false
            })
        },
        filters:{
            toDay:function(val){
                return new Date(val).getDate();  //将时间戳转换成日期
            },
            toWeek:function(val) {
                switch (val) {
                    case 1:
                        return '一 '
                        break;
                    case 2:
                        return '二'
                        break;
                    case 3:
                        return '三'
                        break;
                    case 4:
                        return '四'
                        break;
                    case 5:
                        return '五'
                        break;
                    case 6:
                        return '六'
                        break;
                    case 7:
                        return '日'
                        break;
                    default:
                        return 'NULL'
                }
            },
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" rel="stylesheet/scss" scoped>
  @import "../style/iconfont/iconfont.css";
  $primaryColor: #64cabc;
  //$primaryColor: #64cabc;
  .thisPickInput{
    border: 0;
    padding: 4px;
    border-radius: 5px;
    width: 100%;
  }
  .DatePickerBox{
    display: inline-block;
    padding: 10px;
    background: rgb(244,244,244);
    -webkit-box-shadow: 0px 0px 1px #666;
    -moz-box-shadow: 0px 0px 1px #666    ;
    box-shadow: 0px 0px 1px #666    ;
  }
  .DatePickerBoxPosition{
    position: absolute;
    left: 0px;
    top: 100%;
    z-index: 9999;
  }
  .DatePicker{
    font-family: '微软雅黑';
    color: #333;
    width: 315px;
    margin: 0 auto;

  }
  .DatePicker ,.DatePicker *{
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
  }
  .DatePicker .DatePickerTop .showData,
  .DatePicker .DatePickerTop .control{
    display: inline-block;
    text-align: center;
    cursor: pointer;
  }
  .DatePicker .DatePickerTop{
    padding: 7px 0;
  }
  .DatePicker .DatePickerTop .control{
    color: $primaryColor;
    font-size: 20px;
    font-weight: bold;
    width: 30px;
  }
  .DatePicker .DatePickerTop .showData{
    font-size: 17px;
    width: 95px;
  }
  .DatePicker .DatePickerWeek{
    border-bottom: solid 1px $primaryColor;
    margin-bottom: 10px;
  }
  .DatePicker  .dayUnit{
    display: inline-block;
    width: 45px;
    height: 45px;
    text-align: center;
    line-height: 43px;
    border: solid 2px transparent;
    position: relative;
  }
  .DatePicker .DatePickerData .dayUnit:hover,
  .DatePicker .DatePickerMonth .dayUnit:hover{
    cursor: pointer;
    border:solid 2px $primaryColor;
  }
  .DatePicker .DatePickerData .noNowMonth{
    color: #C1C0C0;
  }
  .DatePicker .DatePickerData .pickerDay{
    background: $primaryColor;
    border:solid 2px $primaryColor;
    color: #fff;
  }
  .DatePicker .DatePickerData .pickerDay:hover{
    border:solid 2px $primaryColor;
  }
  .DatePicker  .toDay{
    background: $primaryColor;
    position: absolute;
    font-size: 12px;
    border-radius: 50%;
    width: 20px;
    height: 20px;
    color: #fff;
    line-height: 20px;
    left: 0;
    -webkit-transform-origin:0 0;
    -webkit-transform: scale(0.8);
    top: 0;
  }
  .DatePickerMonth{
    position: relative;
  }
  .DatePickerMonth .monthUnit{
    width: 25%;
    position: relative;
    display: inline-block;
    text-align: center;
    height: 78px;
    line-height: 78px;
  }
  .DatePickerMonth .choseYear{
    position: absolute;
    z-index: 2;
    top: 50%;
    color: $primaryColor;
    font-size: 20px;
    font-weight: bold;
    margin-top: -11px;
    cursor: pointer;

  }
</style>
