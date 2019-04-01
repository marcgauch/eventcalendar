<template>
  <div id="calendar">
    <div id="dateholderOuter">
      <div id="monthAndYearDisplay">{{monthAndYearDisplay}}</div>
      <b-table :fields="fields" bordered small dark fixed style="margin-bottom: 0; cursor: default;"></b-table>
      <div id="dateholderInner">
        <b-table :fields="fields" :items="items" bordered small dark fixed thead-class="hidden_header" >
          <template slot="MON" slot-scope="data">
            <Day
              :data="data.item['MON']" 
              v-on:became-active="becameActive($event)"
              v-on:became-clicked="becameClicked($event)"
            />
          </template>
          <template slot="TUE" slot-scope="data">
            <Day
              :data="data.item['TUE']"
              v-on:became-active="becameActive($event)"
              v-on:became-clicked="becameClicked($event)"
            />
          </template>
          <template slot="WED" slot-scope="data">
            <Day
              :data="data.item['WED']"  
              v-on:became-active="becameActive($event)"
              v-on:became-clicked="becameClicked($event)"
            />
          </template>
          <template slot="THU" slot-scope="data">
            <Day
              :data="data.item['THU']"
              v-on:became-active="becameActive($event)"
              v-on:became-clicked="becameClicked($event)"
            />
          </template>
          <template slot="FRI" slot-scope="data">
            <Day
              :data="data.item['FRI']"
              v-on:became-active="becameActive($event)"
              v-on:became-clicked="becameClicked($event)"
            />
          </template>
          <template slot="SAT" slot-scope="data">
            <Day
              :data="data.item['SAT']"
              v-on:became-active="becameActive($event)"
              v-on:became-clicked="becameClicked($event)"
            />
          </template>
          <template slot="SUN" slot-scope="data">
            <Day 
              :data="data.item['SUN']"
              v-on:became-active="becameActive($event)"
              v-on:became-clicked="becameClicked($event)"
            />
          </template>
        </b-table>
      </div>
    </div>
  </div>
</template>

<script>
import Day from "./Day";

export default {
  name: "Calendar",
  components: {
    Day
  },
  data() {
    return {
      calendarEntries: {},
      startDate: undefined,
      months: ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"],
      monthAndYearDisplay: undefined, // title of calendar [April 2019]
      activeMonth: undefined, // [0-11]
      activeYear: undefined, // [yyyy]
      fields: [ //title for table
        { key: "MON", label: "MO", class: 'text-center'},
        { key: "TUE", label: "TU", class: 'text-center'},
        { key: "WED", label: "WE", class: 'text-center'},
        { key: "THU", label: "TH", class: 'text-center'},
        { key: "FRI", label: "FR", class: 'text-center'},
        { key: "SAT", label: "SA", class: 'text-center'},
        { key: "SUN", label: "SU", class: 'text-center'},
      ],
      events: [],
      items: [],
    };
  },
  methods: {
    //click handler
    becameActive(date){
      if (date.month !== undefined && date.year && (date.month != this.activeMonth || date.year != this.activeYear)){
        this.recalculateColors(date.year, date.month);
      }
    },
    becameClicked(event){
      console.log("CLICKED AT: " + this.formatDate(new Date(event.year, event.month, event.day)));
    },

    loadData(){
      //this should be filled by ajax
      this.calendarEntries =  {
        "2019-05-03": 7,
        "2018-02-08": 4,
        "2019-02-11": 1,
        "2019-02-15": 14,
        "2019-02-20": 12,
        "2019-02-21": 5,
        "2019-02-24": 3,
        "2019-03-01": 7,
        "2019-03-04": 0,
        "2019-03-12": 777,
        "2019-03-13": 17,
        "2019-03-20": 9,
        "2019-03-23": 55,
        "2019-03-27": 8
      }
      //pick the earliest date
      this.startDate = new Date(
        Object.keys(this.calendarEntries).reduce(
          function (pre, cur) {
            return Date.parse(pre) > Date.parse(cur) ? cur : pre;
          }
        )
      );
    },
    
    calculate() {
      var end = new Date(); // today
      this.events = []; //clear events

      //fill with actual events
      for (let d = new Date(this.startDate); d < end; d.setDate(d.getDate() + 1)) {
        this.events.push({day: d.getDate(), month: d.getMonth(), year: d.getFullYear(), count: this.getAlertsForDate(this.formatDate(d))})
      }
      
      //add elements at the end of the array. some are to space it out and some to not get the array out of bound exception
      for (let i = 0; i < 14; i++){
        this.events.push({});
      }
      //fill the start of the array so that the first real element matches its position in the calendar
      let startday = this.startDate.getDay()
      for (let i = 1; i < startday; i++){
        let d = new Date(this.startDate);
        d.setDate(d.getDate() - i);
        this.events.unshift({day: d.getDate(), month: d.getMonth(), year: d.getFullYear(), count: 0});
      }
    },

    recalculateColors(year, month){
      this.activeYear = year;
      this.activeMonth = month;
      this.monthAndYearDisplay = this.months[month] + " " + year; // display month and year
      //cut it into chunks of 7 and add style if its the active month
      this.items.length = 0;
      const length = this.events.length -7;
        for (let i = 0; i < length; i+=7){
          this.items.push({
            "MON": this.events[i  ],
            "TUE": this.events[i+1],
            "WED": this.events[i+2],
            "THU": this.events[i+3],
            "FRI": this.events[i+4],
            "SAT": this.events[i+5],
            "SUN": this.events[i+6],
            _cellVariants: { // for styling
              "MON": this.events[i  ].month == month ? 'primary' : '',
              "TUE": this.events[i+1].month == month ? 'primary' : '',
              "WED": this.events[i+2].month == month ? 'primary' : '',
              "THU": this.events[i+3].month == month ? 'primary' : '',
              "FRI": this.events[i+4].month == month ? 'primary' : '',
              "SAT": this.events[i+5].month == month ? 'primary' : '',
              "SUN": this.events[i+6].month == month ? 'primary' : '',
            }
          })
        }

    },
    getAlertsForDate(d) {
      return this.calendarEntries[d] ? this.calendarEntries[d] : 0; // ich mag undefined einfach nicht
    },
    formatDate(date) {
      //formats any date into yyyy-MM-dd
      var d = new Date(date),
          month = '' + (d.getMonth() + 1),
          day = '' + d.getDate(),
          year = d.getFullYear();

      if (month.length < 2) month = '0' + month;
      if (day.length < 2) day = '0' + day;

      return [year, month, day].join('-');
    }
  },
  created() {
    //load Data
    this.loadData();

    //fill and behave as if the user wanted the last date
    let d = new Date();
    this.calculate();
    this.recalculateColors(d.getFullYear(), d.getMonth());
  },
  mounted(){
    //scroll down
    let container = this.$el.querySelector("#dateholderInner");
    container.scrollTop = container.scrollHeight;
  }
};
</script>

<style scoped>
#dateholderOuter{
  width: 250px;
  height: 600px;
  overflow: hidden;
}
#dateholderInner{
  width: 267px;
  height: 100%;
  overflow-y: scroll;
  box-sizing: content-box;
  background-color: #343a40;
}

#monthAndYearDisplay{
  width: 250px;
  height: 35px;
  background-color:#343a40;
  color: white;
  border: 1px solid rgb(69, 77, 85);
  text-align: center;
  vertical-align: bottom;
  display: inline-block;
  font-weight: 700;
  font-size: 16px;
  line-height: 35px;
  padding: 0;
  cursor: default;
}

</style>

<style>
.hidden_header{
 display: none;
}
tr td{
  padding: 0 !important;
  margin: 0 !important;
  cursor: pointer;
}
</style>

