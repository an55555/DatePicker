# DatePicker
A simple TimePicker component for VUE
## Status
Now TimePicker with only the most choice of time .The function is not comprehensive enough
In my options,it should have these features:
1. Can choose the specific time
2. Can choose today quickly
3. Restricted date range 
These features will be improved in later versions
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
<date-picker v-model="item.value" ></date-picker>
```
Use v-model for data binding