<template>
  <div id='App' class="mywk__calendar">
    <p class="tips">日历类型--计算单月数据</p>
    <div class="calendar">
      <div class="calendar__header">
          <div class="header__pre" @click="handlePreMonth"></div>
          <div class="header__title">
            {{selectedYear}}年{{selectedMonth + 1}}月{{selectedDate}}日
          </div>
          <div class="header__next" @click="handleNextMonth"></div>
      </div>

      <div class="calendar__main">
        <div v-for="(item, index) in calendarHeader" :key="index" class="main__block-head">{{item}}</div>

        <div v-for="(dayCell, index) in daysCurentMonth" :key="dayCell.type + dayCell.day + `${index}`"
          :class="`main__block ${dayCell.day === selectedDate && 'main__block-today'}`"
          @click="handleDayClick(dayCell)">
          {{dayCell.day}}
        </div>
      </div>
    </div>

    <p class="tips">日历类型--计算整年数据</p>
    <div class="calendar">
      <div class="calendar__header">
          <div class="header__pre" @click="handlePreMonth"></div>
          <div class="header__title">
            {{selectedYear}}年{{selectedMonth + 1}}月{{selectedDate}}日
          </div>
          <div class="header__next" @click="handleNextMonth"></div>
      </div>

      <div class="calendar__main">
        <div class="main__block-head" v-for="(item, index) in calendarHeader" :key="index">{{item}}</div>

        <div v-for="(dayCell, index) in daysPerMonth[selectedMonth]" :key="dayCell.type + dayCell.day + `${index}`"
         :class="`main__block ${(dayCell.type === 'pre' || dayCell.type === 'next') ? 'main__block-not' : ''} ${(dayCell.day === selectedDate && dayCell.type === 'normal') && 'main__block-today'}`"
         @click="handleDayClick(dayCell)">
          {{dayCell.day}}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      calendarHeader: ["日", "一", "二", "三", "四", "五", "六"],
      selectedYear: new Date().getFullYear(),
      selectedMonth: new Date().getMonth(),
      selectedDate: new Date().getDate()
    };
  },
  computed: {
    daysCurentMonth () {
      const year = this.selectedYear
      const month = this.selectedMonth
      //定义每个月的天数，如果是闰年第二月改为29天
      let daysPerMonth = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];

      if ((year % 4 === 0 && year % 100 !== 0) || year % 400 === 0) {
        daysPerMonth[1] = 29;
      }
      let targetDay = new Date(year, month, 1).getDay();
      let total_calendar_list = [];
      let preNum = targetDay;
      let nextNum = 0;
      if (targetDay > 0) {
        for (let i = 0; i < preNum; i++) {
          let obj = {
            type: "pre",
            day: ""
          };
          total_calendar_list.push(obj);
        }
      }
      for (let i = 0; i < daysPerMonth[month]; i++) {
        let obj = {
          type: "normal",
          day: i + 1
        };
        total_calendar_list.push(obj);
      }

      nextNum = 6 - new Date(year, month+1, 0).getDay()
      
      for (let i = 0; i < nextNum; i++) {
        let obj = {
          type: "next",
          day: ""
        };
        total_calendar_list.push(obj);
      }
      return total_calendar_list;
    },
    daysPerMonth () {
      const year = this.selectedYear
      //定义每个月的天数，如果是闰年第二月改为29天
      let daysPerMonth = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
      if ((year % 4 === 0 && year % 100 !== 0) || year % 400 === 0) {
        daysPerMonth[1] = 29;
      }

      const getFirstOrLastDayFromMonth = new Array(12).fill(null).map((_, index) => {
        return {
          firstDay: new Date(year, index, 1).getDay(),
          lastDay: new Date(year, index, daysPerMonth[index]).getDay()
        }
      });

      return new Array(12).fill([]).map((_, month) => {
          let monthDate = [];
          // 上个月的最后几天
          const lastMonthDays = daysPerMonth[month === 0 ? 11 : month - 1]
          for (let date = lastMonthDays; date > lastMonthDays - getFirstOrLastDayFromMonth[month].firstDay; date--) {
            monthDate.unshift({
              day: date,
              type: "pre"
            });
          }
          // 本月日期
          for (let i = 0; i < daysPerMonth[month]; ) {
            monthDate.push({
              day: ++i,
              type: "normal"
            });
          }
          // 下个月的头几天
          for (let i = 1; i < 7 - getFirstOrLastDayFromMonth[month].lastDay; i++) {
            monthDate.push({
              day: i,
              type: "next"
            });
          }
          return monthDate;
        }
      )
    }
  },
  methods: {
    handleDayClick(dayCell) {
      switch (dayCell.type) {
        case 'pre':
          this.handlePreMonth()
          break
        case 'normal':
          // do anything...
          this.selectedDate = Number(dayCell.day)
          break
        case 'next':
          this.handleNextMonth()
          break
      }
    },
    handlePreMonth() {
      if (this.selectedMonth === 0) {
        this.selectedYear -= 1
        this.selectedMonth = 11
      } else {
        this.selectedMonth -= 1
      }
      this.selectedDate = 1
    },
    handleNextMonth() {
      if (this.selectedMonth === 11) {
        this.selectedYear += 1
        this.selectedMonth = 0
      } else {
        this.selectedMonth += 1
      }
      this.selectedDate = 1
    }
  }
};
</script>

<style>
.mywk__calendar {
  width: 100%;
  max-width: 480px;
  margin: 0 auto;
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
}

.tips {
  margin: 15px 0 0 0;
  text-align: center;
}

.calendar {
  flex-shrink: 0;
  width: 94.6666666667vw;
  max-width: 454.4px;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 15px 0 20px 0;
  border-radius: 4px;
  background-color: white;
  box-shadow: 0 0 10px rgba(208, 208, 208, 0.5);
}
.calendar .calendar__header {
  color: #2c3135;
  font-size: 16px;
  width: 84vw;
  max-width: 403.2px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  line-height: 22px;
  margin-top: 17px;
}
.calendar .calendar__header .header__title {
  font-size: 16px;
  letter-spacing: 1px;
}
.calendar .calendar__header .header__pre {
  height: 12px;
  width: 12px;
  position: relative;
}
.calendar .calendar__header .header__pre:after {
  content: " ";
  display: inline-block;
  height: 9px;
  width: 9px;
  border-width: 2px 2px 0 0;
  border-color: #c8c8cd;
  border-style: solid;
  transform: matrix(0.71, 0.71, -0.71, 0.71, 0, 0) rotate(180deg);
  position: absolute;
  top: 50%;
  margin-top: -4px;
  right: 2px;
}
.calendar .calendar__header .header__next {
  height: 12px;
  width: 12px;
  position: relative;
}
.calendar .calendar__header .header__next:after {
  content: " ";
  display: inline-block;
  height: 9px;
  width: 9px;
  border-width: 2px 2px 0 0;
  border-color: #c8c8cd;
  border-style: solid;
  transform: matrix(0.71, 0.71, -0.71, 0.71, 0, 0);
  position: absolute;
  top: 50%;
  margin-top: -4px;
  right: 2px;
}
.calendar .calendar__main {
  width: 84vw;
  max-width: 403.2px;
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
  padding-top: 19px;
}
.calendar .calendar__main .main__block {
  width: 11.7333333333vw;
  height: 11.7333333333vw;
  max-width: 56.32px;
  max-height: 56.32px;
  margin-bottom: 15px;
  border-radius: 2px;
  font-size: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #666666;
  background-color: #fff;
  flex-shrink: 0;
  box-shadow: 0;
  position: relative;
}
.calendar .calendar__main .main__block .main__block-piont {
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background-color: #f93d3a;
  position: absolute;
  left: calc(50% - 2.5px);
  bottom: 0;
}
.calendar .calendar__main .main__block-not {
  background-color: #edf2f5;
  color: #7f8fa4;
}
.calendar .calendar__main .main__block-today {
  transition: 0.5s all;
  background-color: #f93d3a;
  color: #fff;
  box-shadow: 0 2px 6px rgba(171, 171, 171, 0.5);
}
.calendar .calendar__main .main__block-head {
  width: 11.7333333333vw;
  height: 11.7333333333vw;
  max-width: 56.32px;
  max-height: 56.32px;
  margin-bottom: 15px;
  border-radius: 2px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 12px;
  color: #7f8fa4;
  background-color: #fff;
  flex-shrink: 0;
}
</style>
