<template>
  <div class="date-picker">
    <input type="text" v-model="showValue" readonly="true" @click="togglePick">
    <div class="date-picker-wrap" v-show="show">
      <table>
        <thead>
          <tr class="date-head">
            <th colspan="4">
              <span class="ban-lt" @click="yearClick(-1)">&lt;</span>
              <span class="show-year">{{now.getFullYear()}}</span>
              <span class="ban-gt" @click="yearClick(1)">&gt;</span>
            </th>
            <th colspan="3">
              <span class="ban-lt" @click="monthClick(-1)">&lt;</span>
              <span class="show-month">{{months[now.getMonth()]}}</span>
              <span class="ban-gt" @click="monthClick(1)">&gt;</span>
            </th>
          </tr>
          <tr class="date-days">
            <th v-for="item in days">{{item}}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="i in 6">
            <td v-for="j in 7" :class="date[(i-1)*7+j-1]&&date[(i-1)*7+j-1].status" @click="pickDate(i,j)">{{date[(i-1)*7+j-1]&&(date[(i-1)*7+j-1].text)}}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default{
  props: {
    width: { type: String, default: '238px' },
    readonly: { type: Boolean, default: true },
    value: { type: String, default: '' },
    format: { type: String, default: 'YYYY-MM-DD' },
    styleObj: {type: Object, default: null}
  },
  data () {
    return {
      show: false,
      showValue: '',
      days: ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa'],
      months: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
      date: [],
      now: new Date(new Date().setDate(1))
    };
  },
    watch: {
      show(){
        this.update();
      }
    },
  methods:{
    update(){
      var arr=[];
      var time=new Date(this.now);
      time.setDate(1); //本月第一天
      var curFirstDay=time.getDay();
      curFirstDay === 0 && (curFirstDay = 7);
      time.setDate(0);
      var lastDate=time.getDate();
      for(let i=curFirstDay;i>0;i--){
        arr.push({
          text: lastDate-i+1,
          time: new Date(time.getFullYear(),time.getMonth(),lastDate-i+1),
          status:'date-pass'
        })
      }

      time.setMonth(time.getMonth()+2,0);
      var curLastDate=time.getDate();
      time.setDate(1);
      for(let i=0;i<curLastDate;i++){
        let tempTime=new Date(time.getFullYear(),time.getMonth(),i+1);
        let status="";
        this.stringFy(tempTime)===this.stringFy(new Date())&&(status="date-today");
        arr.push({
          text: i+1,
          time: tempTime,
          status:status
        })
      }

      var lengthArr=arr.length;
      for(let j=1;j<=42-lengthArr;j++){
        arr.push({
          text:j,
          time:new Date(time.getFullYear(),time.getMonth()+1,j),
          status:'date-future'
        })
      }
      this.date=arr;
    },
    yearClick(flag){
      this.now.setFullYear(this.now.getFullYear()+flag);
      this.update();
    },
    monthClick(flag){
      this.now.setMonth(this.now.getMonth()+flag);
      this.update();
    },
    stringFy(time,format=this.format){
      var year=time.getFullYear();
      var month=time.getMonth()+1;
      var date=time.getDate();

      var map={
        YYYY: year,
        MM: ("0"+month).slice(-2),
        DD: ("0"+date).slice(-2)
      }
      return format.replace(/Y+|M+|D+/g,function(str){
        return map[str];
      });
    },
    pickDate(i,j){
      let pickedDate=this.date[(i-1)*7+j-1].time;
      this.showValue=this.stringFy(pickedDate);
      this.now.setFullYear(pickedDate.getFullYear());
      this.now.setMonth(pickedDate.getMonth());
      this.update();
      this.show=false;
    },
    togglePick(){
      this.show=!this.show
    }
  }
}
</script>

<style scoped>
.date-picker{
    display: inline-block;
    position: relative;
}
.date-picker *{
    box-sizing: border-box;
}
.date-picker input {
    width: 100%;
    padding: 5px 10px;
    height: 30px;
    outline: 0 none;
    border: 1px solid #ccc;
    font-size: 13px;
}
.date-picker-wrap{
  position: absolute;
    z-index: 1000;
    width: 238px;
    margin-top: 2px;
    background-color: #fff;
    box-shadow: 0 0 6px #ccc;
}
.date-picker-wrap table{
    width: 100%;
    border-collapse:separate;
    text-align: center;
    font-size: 13px;
    border-spacing: 0;
}
.date-picker-wrap tr{
  height: 32px;
}
.date-picker-wrap th, .date-picker-wrap td {
    width: 34px;
    height: 32px;
    border: 1px solid transparent;
    line-height: 32px;
    text-align: center;
}
.date-picker-wrap td{
    cursor:default;
}
.date-picker-wrap td:hover {
    border:1px solid #7bdcf7;
    border-radius: 10px;
    color:#7bdcf7;
}
.date-picker-wrap td.date-pass:hover,
.date-picker-wrap td.date-future:hover{
    border:1px solid #7bdcf7;
    border-radius: 10px;
    color:#aaa;
}
.date-picker-wrap td.date-today{
    color:#ff5252;
    border:1px solid #7bdcf7;
    border-radius: 10px;
}
.date-picker-wrap td.date-today:hover{
    color:#7bdcf7;
}
.date-picker-wrap .date-head {
    background-color: #3bb4f2;
    text-align: center;
    color: #fff;
    font-size: 14px;
}
.date-picker-wrap .date-days {
    color: #3bb4f2;
    font-size: 14px;
}
.ban-lt,.ban-gt{
  cursor: pointer;
    display: inline-block;
    padding: 0 10px;
    vertical-align: middle;
}
.ban-lt:hover,
.ban-gt:hover {
    background: rgba(16, 160, 234, 0.5);
}
.show-year{
    display: inline-block;
    min-width: 62px;
    vertical-align: middle;
}
.show-month{
    display: inline-block;
    min-width: 28px;
    vertical-align: middle;
}
.date-pass,.date-future{
    color: #aaa;
}
</style>