<template>
  <div style="position: relative;width: 100%;height: 100%;"  @selectstart.prevent>
    <div style="position: relative;width: 100%;height: 100%;">
      <input type="text"  v-model="showModelDate"  @click="showDatePickerBox=!showDatePickerBox;getValue()" :class="modelClass?modelClass:'thisPickInput'" readonly="true"  :placeholder="placeholder?placeholder:'请选择日期'"><br>
      <div @click.stop class="DatePickerBox DatePickerBoxPosition" v-show="showDatePickerBox">
        <div  class="DatePicker">
          <div class="DatePickerTop">
            <span @click="year--"  class="iconfont control">&#xe697;</span><span class="showData"  @click="choseYear=year;showChoseMonth=showChoseDate=false;showChoseYear=true;">{{year}}</span><span @click="year++" class="iconfont control">&#xe6a7;</span>
            <span @click="month--" class="iconfont control">&#xe697;</span><span  class="showData" @click="showChoseYear=showChoseDate=false;showChoseMonth=true;">{{month+1}}月</span><span @click="month++" class="iconfont control">&#xe6a7;</span>
          </div>
          <!--今天是{{year}}年{{month+1}}月{{day}}日星期{{week}}有{{monthDayNum}}天1号是星期{{firstWeek}}表格第一天的时间戳是{{firstStamp}}-->
          <div class="DatePickerWeek" v-show="showChoseDate">
            <span v-for="item in 7" class="dayUnit">{{item|toWeek}}</span>
          </div>
          <div class="DatePickerData" v-show="showChoseDate">
            <span v-for="item in dateList" @click="isForbid(item)?'':getDate(item)" class="dayUnit" :title="item"
                  :class="{
                  pickerDay:new Date(item).toDateString() === new Date(modeStamp).toDateString(),
                  noNowMonth:new Date(item).getMonth() !=month,
                  forbidChose:isForbid(item)
                  }">
                {{item|toDay}}
                <a class="toDay" v-if="new Date(item).toDateString() === new Date().toDateString()">今</a>
            </span>
          </div>
          <div style="text-align: right;padding: 0 12px" v-show="showChoseDate">
              <span v-if="getTime" style="display: inline-block;float: left">
                <input v-model="hour" @blur="changeHour" type="number" min="0" max="23" class="numberInput">:
                <input v-model="minutes" @blur="changeHinters" type="number" min="0" max="59" class="numberInput">:
                <input v-model="seconds" @blur="changeSeconds" type="number" min="0" max="59" class="numberInput">
              </span>
            <span class="dateOperateBtn" @click="clearTime">清空</span>
            <span class="dateOperateBtn" @click="choseToday">今天</span>
            <span class="dateOperateBtn" @click="getDateBtn" v-if="useConfirmBtn">确认</span>
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
        props: ['value','modelClass','placeholder','dataFormat','useConfirm','showTime','useStamp','maxTime','minTime'],
        data () {
            return {
                format:this.dataFormat?this.dataFormat:"YYYY-MM-DD hh:mm:ss",
                modelDate:this.value,  //绑定的日期
                useConfirmBtn:this.useConfirm=='true'?true:false, //是否需要点了确定按钮再退出
                getTime:this.showTime=='true'?true:false,
                modeStamp:'',  //选择的时间戳
                showDatePickerBox:false,  //日期选择控件状态显示
                today:new Date().getDate(),//当前日
                year:new Date().getFullYear(), //当前年份
                month:new Date().getMonth(),//当前月份
                week:new Date().getDay(),//当前是星期几
                day:new Date().getDate(),//当前日
                hour:this.formatDateHour(this.value)||new Date().getHours(),//当前时
                minutes:this.formatDateMinutes(this.value)||new Date().getMinutes(), //当前分
                seconds:this.formatDateSeconds(this.value)||new Date().getSeconds(),//当前秒
                monthDayNum:new Date(new Date().getFullYear(),new Date().getMonth()+1,0).getDate(),//当前月份天数
                firstWeek:1,//当前月份一号是星期几
                firstStamp:1,//当前表格第一格的时间戳
                firstDayStamp:1,//当前1号时间戳
                dayStamp:24*60*60*1000 ,//一天的时间戳
                choseYear:new Date().getFullYear(), //弹出年份选择
                showChoseDate:true,  //日选择
                showChoseMonth:false,//月选择
                showChoseYear:false,//年选择
            }
        },
        methods:{
            //年月改变时   限制年在1-12   限制年不小于1000年
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
            //数字格式为两位数组成
            filterDataNum:function (val) {
                return val<10?'0'+val:val
            },
            getFirstStamp:function(){  //获取表格第一天的时间戳
                var timeDate=this.year+'-'+(this.month+1)+'-1 0:0:0'  //当月1号的日期格式
                this.firstDayStamp = Date.parse(new Date(timeDate)); //指定日期的时间戳
                this.firstWeek=new Date(this.year,this.month,1).getDay();//当时间改变时获取改变时间后的第一天的星期
                this.firstStamp=this.firstDayStamp-(this.firstWeek==0?6:this.firstWeek-1)*this.dayStamp;//当表格第一格的时间戳
            },
            getDate:function (data) {
                this.modeStamp=data
                if(!this.useConfirmBtn){
                    this.getDateBtn()
                    return
                }
                /*如果选择的日期是限制日期，时间要重新判断一次*/
                if((new Date(this.modeStamp).toDateString()===new Date(this.getMaxTime).toDateString())||(new Date(this.modeStamp).toDateString()===new Date(this.getMinTime).toDateString())){
                    this.changeHinters()
                    this.changeSeconds()
                    this.changeHour()
                }
//                this.modelDate=this.filterDataNum(this.formatDateYear(data))+'.'+this.filterDataNum((this.formatDateMonth(data)+1))+'.'+this.filterDataNum(this.formatDateDate(data));
//                this.modelDate=this.Format(data);
//                this.$emit('input', this.modelDate)
//                this.showDatePickerBox=false
            },
//            清空日期
          clearTime:function(){
              this.modelDate='';
              this.modeStamp='';
              if(!this.useConfirmBtn){
                  this.getDateBtn()
              }
          },
//            选择今天
            choseToday:function(){
                this.getNowDate();
                this.modeStamp=Date.parse(new Date())
                if(!this.useConfirmBtn){
                    this.getDateBtn()
                }
            },
            //获取选中的是日期
            getDateBtn:function () { //点击确认按钮
                var getTime=this.Format(this.modeStamp,"YYYY-MM-DD")  //获取选择时间时间的日期（只要日）
                if(this.showTime){
                    getTime+=" "+this.formatTime(this.hour)+":"+this.formatTime(this.minutes)+":"+this.formatTime(this.seconds) //然后加上具体的时间
                }
                var stamp=!this.useStamp?getTime:Date.parse(new Date(getTime))
                this.$emit('input', stamp)
                this.showDatePickerBox=false
            },
            getPickerMonth:function (month) {

            },
            formatDateYear:function(val) {return new Date(val).getFullYear()},
            formatDateMonth:function(val) {return new Date(val).getMonth()},
            formatDateDate:function(val) {return new Date(val).getDate()},
            formatDateHour:function(val) {return new Date(val).getHours()},
            formatDateMinutes:function(val) {return new Date(val).getMinutes()},
            formatDateSeconds:function(val) {return new Date(val).getSeconds()},
/*            Format:function (stamps) {
                if(stamps==''||!stamps){
                    return ''
                }
                var fmt=this.format
//                var getDate=stamps==''||!stamps?new Date():new Date(stamps)
                var getDate=new Date(stamps);
                var o = {
                    "M+": getDate.getMonth() + 1, //月份
                    "D+": getDate.getDate(), //日
                    "h+": getDate.getHours(), //小时
                    "m+": getDate.getMinutes(), //分
                    "s+": getDate.getSeconds(), //秒
                    "q+": Math.floor((getDate.getMonth() + 3) / 3), //季度
                    "S": getDate.getMilliseconds() //毫秒
                };
                if (/(Y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (getDate.getFullYear() + "").substr(4 - RegExp.$1.length));
                for (var k in o)
                    if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
                return fmt;
            },*/
            /*时间戳转日期*/
            Format:function (stamps,format) {  //时间戳转日期
                if(stamps==''||!stamps){
                    return ''
                }
                var fmt=format||this.format
//                var getDate=stamps==''||!stamps?new Date():new Date(stamps)
                var getDate=new Date(stamps);
                var escapeChars = {
                    "YYYY": getDate.getFullYear(), //年份
                    "MM": getDate.getMonth() + 1, //月份
                    "DD": getDate.getDate(), //日
                    "hh": getDate.getHours(), //小时
                    "mm": getDate.getMinutes(), //分
                    "ss": getDate.getSeconds(), //秒
                    "qq": Math.floor((getDate.getMonth() + 3) / 3), //季度
                    "SS": getDate.getMilliseconds() //毫秒
                };
                var fm=fmt.replace(new RegExp(Object.keys(escapeChars).join('|'),'g'),function(match){
                    return escapeChars[match]>=10?escapeChars[match]:'0'+escapeChars[match]
                })
                return fm
            },
            /*个数加0*/
            formatTime:function (num) {
                return num>9?num:'0'+num
            },
            /*时间范围限制*/
            isForbid:function (item) {
                var isMax=false,isMin=false
                if(this.getMinTime){
                    isMin= (item<Date.parse(new Date(this.getMinTime).toDateString()))


                }
                if(this.getMaxTime){
                    isMax= (item>Date.parse(new Date(this.getMaxTime).toDateString()))
                }

                return (isMax||isMin)

            },
            getNowDate:function () {
                this.today=new Date().getDate()//当前日
                this.year=new Date().getFullYear() //当前年份
                this.month=new Date().getMonth()//当前月份
                this.week=new Date().getDay()//当前是星期几
                this.day=new Date().getDate()//当前日
            },
            getValue:function () {
                this.modelDate=this.value
                if(!this.modelDate){
                    this.getNowDate()
                }else{
                    var stringTime=this.modelDate
                    this.modeStamp=stringTime;
                    this.year=this.formatDateYear(stringTime) //当前年份
                    this.month=this.formatDateMonth(stringTime)//当前月份
                    this.week=new Date().getDay()//当前是星期几
                    this.day=this.formatDateDate(stringTime)//当前日
                }
                this.getFirstStamp()
            },
            changeHour:function () {

                var max=23
                var min=0
                if(new Date(this.modeStamp).toDateString()===new Date(this.getMaxTime).toDateString()){
                    max=this.getMaxTime?this.formatDateHour(this.getMaxTime):23
                }
                if(new Date(this.modeStamp).toDateString()===new Date(this.getMinTime).toDateString()){
                    min=this.getMinTime?this.formatDateHour(this.getMinTime):0
                }

                if(this.hour<min){
                    this.hour=min
                }else if(this.hour>max){
                    this.hour=max
                }
            },
            changeHinters:function () {
                var max=59
                var min=0
                if(new Date(this.modeStamp).toDateString()===new Date(this.getMaxTime).toDateString()){
                    max=this.getMaxTime?this.formatDateMinutes(this.getMaxTime):23
                }
                if(new Date(this.modeStamp).toDateString()===new Date(this.getMinTime).toDateString()){
                    min=this.getMinTime?this.formatDateMinutes(this.getMinTime):0
                }
                if(this.minutes<min){
                    this.minutes=min
                }else if(this.minutes>max){
                    this.minutes=max
                }
            },
            changeSeconds:function () {
                var max=59
                var min=0
                if(new Date(this.modeStamp).toDateString()===new Date(this.getMaxTime).toDateString()){
                    max=this.getMaxTime?this.formatDateSeconds(this.getMaxTime):23
                }
                if(new Date(this.modeStamp).toDateString()===new Date(this.getMinTime).toDateString()){
                    min=this.getMinTime?this.formatDateSeconds(this.getMinTime):0
                }

                if(this.seconds<min){
                    this.seconds=min
                }else if(this.seconds>max){
                    this.seconds=max
                }
            },
        },
        computed:{
            'dateList':function(){ //  表格根据firstStampr的变化 而变化
                var dateList=new Array(42)
                for(var i=0; i<42; i++){
                    dateList[i]=(this.firstStamp+i*24*60*60*1000);
                }
                return dateList
            },
            'showModelDate':function () {
                return this.Format(this.modelDate)
            },
            'getMaxTime':function () {
                return this.maxTime?Date.parse(new Date(this.maxTime)):''
            },
            'getMinTime':function () {
                return this.minTime?Date.parse(new Date(this.minTime)):''
            }
        },
        watch:{

            'year':'changeTime',//年月改变时   改变firstStampr
            'month':'changeTime',    //年月改变时   改变firstStampr
    /*        'hour':'changeHour',
            'minutes':'changeHinters',
            'seconds':'changeSeconds',*/
            'value':'getValue'
        },
        mounted:function(){
            this.getValue()
            document.addEventListener('click', (e) => {
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
  /*  $primaryColor: #6aaff7;*/
  $primaryColor: #5ED4CA;
  .thisPickInput{
    width: 100%;
    height: 100%;
    border:0;
    padding: 0 10px;
    cursor: pointer;
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
  .DatePicker .DatePickerData .noNowMonth,
  .DatePicker .DatePickerData .forbidChose{
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
  .DatePicker .DatePickerData .forbidChose:hover{
    border:solid 2px transparent;
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
  .DatePicker .numberInput{
    width: 40px;
  }
  .DatePicker .dateOperateBtn{
    display: inline-block;
    background: $primaryColor;
    color: #fff;
    width: 40px;
    text-align: center;
    margin-left: 5px;
    height: 23px;
    line-height: 23px;
    cursor: pointer;
    border-radius: 2px;
    font-size: 15px;
    position: relative;
    top: 1px;
  }
</style>
