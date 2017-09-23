<template>
    <div id="example">
        <h4>"YYYY-MM-DD"格式</h4>
        <date-picker
                v-model="timePicker1.date"
                placeholder="选择时间"
        ></date-picker>
        <h4>"YYYY-MM-DD hh:mm:ss"格式</h4>
        <date-picker
                v-model="timePicker2.date"
                placeholder="选择时间"
                dateFormat="YYYY-MM-DD hh:mm:ss"
        ></date-picker>
        <h4>通过按键选择</h4>
        <date-picker
                v-model="timePicker3.date"
                placeholder="选择时间"
                useConfirm=true
        ></date-picker>
        <h4>时间范围限制</h4>
        <div class="timeBox">
            <date-picker
                    v-model="timePicker4.minTime"
                    placeholder="开始时间"
                    :maxTime="timePicker4.maxTime"
            ></date-picker>
        </div>
        -
        <div class="timeBox">
            <date-picker
                    v-model="timePicker4.maxTime"
                    placeholder="结束时间"
                    :minTime="timePicker4.minTime"
            ></date-picker>
        </div>

        <h4>时间范围限制(包含时间)</h4>
        <div class="timeBox">
            <date-picker
                    v-model="timePicker5.minTime"
                    placeholder="开始时间"
                    dateFormat="YYYY-MM-DD hh:mm:ss"
                    :maxTime="timePicker5.maxTime"
            ></date-picker>
        </div>
        -
        <div class="timeBox">
            <date-picker
                    v-model="timePicker5.maxTime"
                    placeholder="结束时间"
                    dateFormat="YYYY-MM-DD hh:mm:ss"
                    :minTime="timePicker5.minTime"
            ></date-picker>
        </div>


    </div>
</template>

<script>
    export default {
        components:{
            'date-picker':r=>require.ensure([], () => r(require('./components/DatePicker.vue')), 'DatePicker'),
        },
        data () {
            return {
                msg: 'Hello Wo3rld!',
                time:'',
                maxTime:'',
                minTime:'',
                timePicker1:{
                    date:''
                },
                timePicker2:{
                    date:'1999-11-11 10:04:10',
                },
                timePicker3:{
                    date:'',

                },
                timePicker4:{
                    maxTime:'',
                    minTime:'',
                },
                timePicker5:{
                    maxTime:'',
                    minTime:'',
                }
            }
        }
    }
</script>

<style>
    @import "https://an55555.github.io/CSS-Layout/style-l.css";
    .timeBox{
        display: inline-block;

    }
    h1.sayHello{
        padding: 0 40px;
    }
    #example{
        padding: 0 40px;

    }
</style>