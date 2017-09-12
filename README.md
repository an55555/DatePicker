# DatePicker

> A simple TimePicker component for VUE. Just simple demo for reference only


## Parametes

v-model:Binding data(*)

showTime:`true` or `false` default:`'false'` . Whether the time options is displayed

useConfirm:`true` or `false`  default:`'false'` . Whether make time by btn

dataFormat:default`'YYYY-MM-DD hh:mm:ss'` Time format

placeholder:default`请选择日期` 

modelClass:default none .The class used by internal input

useStamp:`true` or `false` default `false` .Returns in timestamp format

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
            showTime=true
            useStamp=true
            useConfirm=true>
</date-picker>
```
Use v-model for data binding
