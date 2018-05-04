<template>
  <div class="hello">
    <div class="bg"></div>
    <div class="calendar_wrap">
      <div class="top_operate clear">
        <div class="left" @click="changeMonth('prev')"></div>
        <div class="content">{{showDate}}</div>
        <div class="right" @click="changeMonth('next')"></div>
      </div>
      <ul class="week_day clear">
        <li v-for="item in weekDayTitle">{{item}}</li>
      </ul>
      <ul class="date_wrap clear">
        <li v-for="(item,index) in dateDay" :class="{'border_none' : index >= 35,'current_month':item.isCurrentMonth && times < 3,'stop_visit':item.stopVisit && times < 3}" @click="changeState(item.stopVisit,item.canEdit,index,item.date)">
          <p>{{item.date}}</p>
          <p v-if="item.visitDay">{{item.stopVisit ? "停诊" : '出诊'}}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      weekDayTitle: ['日','一','二','三','四','五','六'],
      dateDay:[],
      currentYear:'',
      currentMonth:'',
      currentDay:'',
      showDate:'',
      visitDay:[0,3,4],            // 出诊是星期几 - 数据是后台给的
      times:0,
      stopVisit:[[2,3,16,20,24],[3,6,7],[1,4,5,25,26]]     // 停诊日期 - 数据是后台给的
    }
  },
  methods:{
    getDate(){
      let CurrentDate = new Date();
      this.currentYear = CurrentDate.getFullYear();
      this.currentMonth = CurrentDate.getMonth() + 1;
      this.currentDay = CurrentDate.getDate();
    },
    createCalendar(){
      this.dateDay = [];
      let date =  new Date(this.currentYear,this.currentMonth-1,1);
      let year = date.getFullYear();
      let month = date.getMonth() + 1;
      let day = date.getDate();
      let weekDay = date.getDay();      // 星期几
      this.showDate = year + "年" + (month > 9 ? month : "0" + month) + "月";

      for(let i = 0; i < 42;i++){
        let _this_day = new Date(this.currentYear,this.currentMonth-1,i+1-weekDay);    // 第i个框框的时间
        let _this_year = _this_day.getFullYear();                                       // 第i个框框是哪一年
        let _this_month = _this_day.getMonth() + 1;                                     // 第i个框框是哪个月
        let _this_date = _this_day.getDate();                                           // 第i个框框是哪一天
        let _this_week = _this_day.getDay();                                             // 第i个框框是星期几

        // 判断这一天（周几）是不是要出诊
        if(this.visitDay.includes(_this_week)){
          // 判断是不是当前月
          if(_this_year = year && _this_month == month){
            if(this.times < 3){
              if(this.stopVisit[this.times].includes(_this_date)){
                this.dateDay.push({
                  date:_this_date,
                  isCurrentMonth:true,
                  visitDay:true,
                  canEdit:true,
                  stopVisit:true
                })
              }else {
                this.dateDay.push({
                  date:_this_date,
                  isCurrentMonth:true,
                  visitDay:true,
                  canEdit:true,
                  stopVisit:false
                })
              }
            }else {
              this.dateDay.push({
                date:_this_date,
                isCurrentMonth:true,
                visitDay:true,
                canEdit:true,
                stopVisit:false
              })
            }
          }else {
            this.dateDay.push({
              date:_this_date,
              isCurrentMonth:false,
              visitDay:true,
              canEdit:false,
              stopVisit:false
            })
          }
        }else {
          // 判断是不是当前月
          if(_this_year = year && _this_month == month){
            this.dateDay.push({
              date:_this_date,
              isCurrentMonth:true,
              visitDay:false,
              canEdit:false,
              stopVisit:false
            })
          }else {
            this.dateDay.push({
              date:_this_date,
              isCurrentMonth:false,
              visitDay:false,
              canEdit:false,
              stopVisit:false
            })
          }
        }
      }

    },
    changeMonth(str){
      if(str == 'prev'){
        // 我的项目中只允许看今天以及之后的，所以加个判断，而且只能编辑三个月之内的
        if(this.times > 0){
          this.times--;
          this.currentMonth--;
          this.createCalendar();
        }
      }else {
        this.times++;
        this.currentMonth++;
        this.createCalendar();
      }
    },
    changeState(showState,editState,index,date){
      if(editState && this.times < 3){
        if(showState){
          this.dateDay[index].stopVisit = false;
          this.stopVisit[this.times].splice(this.stopVisit[this.times].findIndex(arr => arr === date),1);
        }else {
          this.dateDay[index].stopVisit = true;
          this.stopVisit[this.times].push(date);
        }
      }
    }
  },
  created(){
    this.getDate();
    this.createCalendar();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  @import "../static/home.css";
</style>
