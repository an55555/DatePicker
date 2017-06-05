# DatePicker

> A simple TimePicker component for VUE. Just simple demo for reference only


## Parametes

v-model:Binding data

showTime:'true' or 'false' default:`'false'` . Whether the time options is displayed

useConfirm:'true' or 'false' default:`'false'` . Whether make time by btn

dataFormat:default`'YYYY-MM-DD hh:mm:ss'` Time format

placeholder:default`请选择日期`

## Usage
1.Import the TimePicker component in VUE
```bash
import myDatepicker from '../components/DatePicker.vue'; //引入对应的组件
 components: {
     'date-picker': myDatepicker,
 }

```
2.Use the TimePicker component in page
```bash
 <date-picker v-model="time" useConfirm='true' showTime="true"   placeholder="!自定义placeholder?请选择日期日期"   dataFormat="YYYY-MM-DD"></date-picker>
```
Use v-model for data binding
