# DatePicker

> A simple TimePicker component for VUE. Just simple demo for reference only

**[在线Demo](https://an55555.github.io/DatePicker-VUE/dist/index.html?_blank)**

<a href="http://write.blog.csdn.net/postlist" target="_blank">麦田里的码农</a>

## Parametes

v-model:Binding data(*)

useConfirm:`true` or `false`  default:`'false'` . get date by btn 

dataFormat:default`'YYYY-MM-DD'`. date format

placeholder:default`请选择日期` 

modelClass:default none .The class used by internal input

useStamp:`true` or `false` default `false` .Returns in timestamp format

minTime: default none .default none .Minimum selection time 

maxTime: default none .Maximum selection time 


## Usage
1.Import the TimePicker component in VUE
```javascript
import myDatepicker from '../components/DatePicker.vue'; //引入对应的组件
 components: {
     'date-picker': myDatepicker,
 }

```
2.Use the TimePicker component in page
```html
<date-picker
           v-model="getPageData.arg.startTime"
           modelClass="inputStyle" 
           placeholder="开始时间"
           dataFormat="YYYY-MM-DD hh:mm:ss"
           :maxTime="getPageData.arg.endTime">
</date-picker>
```
