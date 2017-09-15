<template>
    <div id="example">
        <div class="thisInlineBlock" @click="timePicker.format=true">
            <span class="radioBoxBg " :class="timePicker.format?'radioBoxBg_1':'radioBoxBg_0'" ></span><span>"YYYY-MM-DD"格式</span>
        </div>
        <div class="thisInlineBlock" @click="timePicker.format=false">
            <span class="radioBoxBg " :class="timePicker.format?'radioBoxBg_0':'radioBoxBg_1'"></span><span>"YYYY-MM-DD hh:mm:ss"格式</span>
        </div>
        <date-picker
                v-model="minTime"
                placeholder="选择时间"
        ></date-picker>
        <h4>"YYYY-MM-DD hh:mm:ss"格式选择</h4>
        <date-picker
                v-model="minTime"
                placeholder="选择时间"
                dataFormat="YYYY-MM-DD hh:mm:ss"
                showTime=true
                useConfirm=true
        ></date-picker>
        <h4>时间范围限制</h4>
        <div class="timeBox">
            <date-picker
                    v-model="minTime"
                    placeholder="开始时间"
                    dataFormat="YYYY-MM-DD hh:mm:ss"
                    showTime=true
                    useConfirm=true
                    :maxTime="maxTime"
            ></date-picker>
        </div>
        -
        <div class="timeBox">
            <date-picker
                    v-model="maxTime"
                    placeholder="结束时间"
                    dataFormat="YYYY-MM-DD hh:mm:ss"
                    showTime=true
                    useConfirm=true
                    :minTime="minTime"
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
                timePicker:{
                    data:'',
                    format:true,
                    maxTime:'',
                    minTime:'',
                }
            }
        }
    }
</script>

<style scoped>
    @import "https://an55555.github.io/CSS-Layout/style-l.css";
    .timeBox{
        display: inline-block;

    }
    .thisInlineBlock{
        display: inline-block;
        margin-right: 20px;
        cursor: pointer;
    }

    /*.inputStyle{border: solid 1px #eee}*/
</style>