---
layout: post
title: 'js 获取日期方法'
category: root
summary: '获取每月有几周，根据年月周获取该周从周一到周日的日期等方法'
author: hjy
---
## 本文基于react-native
### 本人在用react-native写一个关于课程表的APP时需要课程表按照日期周期显示，网上查了许多方法，都没有达到自己想要的效果，根据一些方法的参考，再根据自己思维写出自己想要的效果如下图所示
![image]({{ "/assets/images/jsGetTime.png" | relative_url }})

* 获取每月有几周（注：从第一个周一开始算该月第一周）

  ```
  getWeeks(year, month) {
    var d = new Date();
    // 该月第一天
    d.setFullYear(year, month-1, 1);
    var w1 = d.getDay();
    if (w1 == 0) w1 = 7;
    // 该月天数
    d.setFullYear(year, month, 0);
    var dd = d.getDate();
    // 第一个周一
    let d1;
    if (w1 != 1) d1 = 7 - w1 + 2;
    else d1 = 1;
    let week_count = Math.ceil((dd-d1+1)/7);
    return week_count;
  }
  ```

* 根据年月周获取该周从周一到周日的日期

  ```
  getWeekTime(year, month,weekday) {
    var d = new Date();
    // 该月第一天
    d.setFullYear(year, month-1, 1);
    var w1 = d.getDay();
    if (w1 == 0) w1 = 7;
    // 该月天数
    d.setFullYear(year, month, 0);
    var dd = d.getDate();
    // 第一个周一
    let d1;
    if (w1 != 1) d1 = 7 - w1 + 2;
    else d1 = 1;
    var monday = d1+(weekday-1)*7;
    var sunday = monday + 6;
    var from = year+"-"+month+"-"+monday;
    var to;
    if (sunday <= dd) {
      to = year+"-"+month+"-"+sunday;
    } else {
      d.setFullYear(year, month-1, sunday);
      let days=d.getDate();
      to = d.getFullYear()+"-"+(d.getMonth()+1)+"-"+days;
    }
    console.log(weekday+" 从" + from + " 到 " + to + "");
  }
  ```

* 获取每月第几周的周一的日期

  ```
  getMondayTime(year, month,weekday) {
    var d = new Date();
    // 该月第一天
    d.setFullYear(year, month-1, 1);
    var w1 = d.getDay();
    if (w1 == 0) w1 = 7;
    // 该月天数
    d.setFullYear(year, month, 0);
    var dd = d.getDate();
    // 第一个周一
    let d1;
    if (w1 != 1) d1 = 7 - w1 + 2;
    else d1 = 1;
    var monday = d1+(weekday-1)*7;
    return monday;
  }
  ```

* 获取周一的日期

  ```
  getMonDate() {
    var d=new Date(),
        day=d.getDay(),
        date=d.getDate();
    if(day==1)
      return d;
    if(day==0)
      d.setDate(date-6);
    else
      d.setDate(date-day+1);
    return d;
  }
  ```

* 获得周期名字

  ```
  getDayName(day) {
    var day=parseInt(day);
    if(isNaN(day) || day<0 || day>6)
      return false;
    var weekday=["周日","周一","周二","周三","周四","周五","周六"];
    return weekday[day];
  }
```

* 获得当前日期在当月第几周

  ```
  //不包括跟上个月重合的部分
  getMonthWeek(a, b, c) {
    var date = new Date(a, parseInt(b) - 1, c), w = date.getDay(), d = date.getDate();
    return Math.ceil(
      (d + 6 - w) / 7
    );
  }
  ```
* 获取当月最后一天日期

  ```
  getLastDay(year,month) {
    var new_year = year;    //取当前的年份
    var new_month = month++;//取下一个月的第一天，方便计算（最后一天不固定）
    if(month>12) {
      new_month -=12;        //月份减
      new_year++;            //年份增
    }
    var new_date = new Date(new_year,new_month,1);                //取当年当月中的第一天
    return (new Date(new_date.getTime()-1000*60*60*24)).getDate();//获取当月最后一天日期
  }
  ```

* 根据当前日期获取该天所在周，若该月1号不是周一，获取该月第一周的周一日期，小于该日期的归为上个月最后一周

  ```
  //当前日期几月第几周
  showDate(){
    var that=this;
    var d=this.getMonDate();
    var ds=new Date();
    var arr=[];
    for(var i=0; i<7; i++) {
      let weekDay=this.getDayName(d.getDay());
      let date=d.getDate()+'日';
      if(weekDay=='周一'){
        let beginTime=ds.getFullYear()+'-'+(d.getMonth()+1)+'-'+d.getDate();
        that.state.beginTime = beginTime;
      }
      if(weekDay=='周日'){
        let endTime=ds.getFullYear()+'-'+(d.getMonth()+1)+'-'+d.getDate();
        that.state.endTime = endTime;
      }
      arr.push({weekDay:weekDay,date:date});
      d.setDate(d.getDate()+1);
    }
    let month=ds.getMonth()+1;
    let weeks=this.getMonthWeek(ds.getFullYear(),month,ds.getDate())-1;
    //每月周一日期
    let oneDate=that.getMondayTime(ds.getFullYear(),month,1);
    if(ds.getDate()<oneDate){
      month=ds.getMonth();
      weeks=this.getWeeks(ds.getFullYear(),month);
    }
    console.log('month:',month,'weeks:',weeks);
    this.setState({
      list:arr,
      yearDate:ds.getFullYear(),
      monthDate:month,
      month:ds.getMonth()+1,
      monthWeek:'第'+weeks+'周',
      theMonthWeek:'第'+weeks+'周'
    });
  }
  ```
