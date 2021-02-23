<template>
<div id="table">
  <div id="up" style="display: flex; width: 700px; margin: auto; margin-bottom: 10px">
    <a id="timetable" target="_blank" href="https://mmf.bsu.by/ru/raspisanie-zanyatij/dnevnoe-otdelenie/3-kurs/2-gruppa/">Timetable</a>
    <button v-on:click="week = oposite(week)">Week <b>{{week}}</b></button> <button v-on:click="group = oposite(group)">Group <b>{{group}}</b></button>
    <input v-model.trim="search" placeholder="поиск">
  </div>
  <table style="margin: auto;">
    <thead>
      <th>day</th>
      <th>time</th>
      <th style="width: 20px"></th>
      <th>subject</th>
      <th>type</th>
    </thead>
    <tbody>
      <template v-for="value, day in data">
        <template v-for="objs, time, ind in value">
          <tr v-bind:class="{ current: is_current(time, day) }" v-for="obj, key in objs" v-if="is_seen(obj)">
            <td v-bind:class="{ active: is_searched(day)} " v-if="is_seen_day(value, obj)"
              :rowspan="nested_len(value, obj)">{{day}}</td>
            <td v-bind:class="{ active: is_searched(time) }" v-if="is_seen_time(objs, obj)" :rowspan="calc(objs)">
              {{time}}</td>
            <td v-bind:class="{ active: is_searched(obj.week + obj.group) }">{{obj.week}} {{obj.group}}</td>
            <td v-bind:class="{ active: is_searched(obj.subject), subject: true }">
                            <template v-if="obj.link!=''"><a :href="obj.link">{{obj.subject}}</a></template>
                            <template v-if="obj.link==''">{{obj.subject}}</template>                       
                        </td>
            <td v-bind:class="{ active: is_searched(obj.type) }">{{obj.type}}</td>    
            <!-- <td>
              <ul>
                <li v-for="item in obj.other">
                  <a target="_blank" v-if="is_link(item)" :href="item[0]">{{item[1]}}</a>
                  <template v-if="!is_link(item)">{{item}}</template>
                </li>
              </ul>
            </td> -->
          </tr>
        </template>
      </template>
      </tr>
    </tbody>
  </table>
</div>
</template>

<script>
const createObject = (week, group, subject, type, link="") => {
  return {
    "week": week, group: group, "subject": subject, "type": type, "link": link
  };
}

const json = {
  "Пон.":
  {
    "13:00–14:20": [
        createObject("", "", `С/м Основы права (с 01.03) 
            Веренчиков И.Р.`, "л")],
    "14:30–15:50": [
        createObject("", "", `Численные методы (с 01.03) 
            Волков В.М.`, "п", "https://edummf.bsu.by/course/view.php?id=466")
      ],
    "16:00–17:20": [
        createObject("1н", "", `Построение и анализ алгоритмов 
            Суздаль`, "л", "https://edummf.bsu.by/course/view.php?id=717"),
        createObject("2н", "", "ИТ Липлялина", "л", "https://edummf.bsu.by/course/view.php?id=890"),
      ],
    "17:30–18:50": [
        createObject("", "", `Теория вероятностей и математическая статистика 
            Леонов В.В.`, "л", "https://edummf.bsu.by/course/view.php?id=885"),
    ],
    "19:00–20:20": [
        createObject("", "а", `Теория вероятностей и математическая статистика
Спасков С.А.`, "п", "https://edummf.bsu.by/course/view.php?id=885"),
        createObject("", "б", `Комбинаторное моделирование и исследование операций
Гулецкий Н.В.`, "п", "https://edummf.bsu.by/course/view.php?id=526"),
    ]
  },
  "Вторник": {
    "11:15–12:35": [
    createObject("", "", `Физическая культура`, ""),
    ],
    "13:00–14:20": [
        
    createObject("", "б", `Построение и анализ данных
Вельченко С.А.`, "п 409"),
      ],
    "14:30–15:50": [
        
    createObject("", "а", `Комбинаторное моделирование и исследование операций
Ромащенко Г.С.`, "п ГеоФ 321"),
    createObject("", "б", `Вариационное исчисление и методы оптимизации
Иванишко И.А.`, "п ФМО 610"),
      ],
    "16:00–17:20": [
        
    createObject("", "", `Теоретическая механика
Докукова Н.А.`, "п 606"),
    ],
    "17:30–18:50": [
       
    createObject("", "а", `Численные методы
Якименко Т.С.`, "п 405"),
    ]
  },
  "Среда": {
    "11:15–12:35": [
    createObject("", "", `Физическая культура`, ""),
    ],
    "14:30–15:50": [
        
    createObject("1н", "", `С/м Основы права (с 01.03)
Веренчиков И.Р.`, "п ФМО 809"),
    createObject("", "б", `Вариационное исчисление и методы оптимизации
Иванишко И.А.`, "ФМО 610"),
      ],
    "16:00–17:20": [
        
    createObject("", "", `ДС Параллельное программирование
Коваленко Н.С.`, "л 609"),
    ],
    "17:30–18:50": [
       
    createObject("1н", "а", `ДС Параллельное программирование
Коваленко Н.С.`, "п 403"),
    createObject("1н", "б", `Информационные технологии
Липлянина В.П.`, "п 409"),
    createObject("2н", "а", `Информационные технологии
Липлянина В.П.`, "п 409"),
    createObject("2н", "б", `ДС Параллельное программирование
Коваленко Н.С.`, "п 403"),
    ],
    "19:00–20:20": [
    createObject("", "а", `Построение и анализ данных
Суздаль С.В.`, "п 409"),
    createObject("", "б", `Теория вероятностей и математическая статистика
Чесалин В.И.`, "п 406"),
    ]
  },
  "Пятница": {
    "13:00–14:20": [
    createObject("1н", "", `Комбинаторное моделирование и исследование операций
Ромащенко Г.С.`, "л", "https://edummf.bsu.by/course/view.php?id=525"),
    createObject("2н", "", `Основы педагогики и психологии (с 05.03)
Петрикевич С.А.`, "п", "https://edummf.bsu.by/course/view.php?id=924"),
    ],
    "14:30–15:50": [    
    createObject("", "", `Теоретическая механика
Савчук В.П.`, "л", "https://edummf.bsu.by/course/view.php?id=322"),
      ],
    "16:00–17:20": [
        
    createObject("", "", `Вариационное исчисление и методы оптимизации
Тыкун А.С.`, "л", "https://edummf.bsu.by/course/view.php?id=904"),
    ],
    "17:30–18:50": [
    createObject("", "а", `Вариационное исчисление и методы оптимизации
Тыкун А.С.`, "п", "https://edummf.bsu.by/course/view.php?id=902"),
    createObject("", "б", `Численные методы
Проконина Е.В.`, "п", "https://edummf.bsu.by/enrol/index.php?id=143"),
    ]
  },
  "Суббота": {
    "08:15–09:35": [
    createObject("", "", `Основы педагогики и психологии
Ершова О.И., Галуза А.В.`, "л", "https://edummf.bsu.by/course/view.php?id=924"),
    ],
    "09:45–11:05": [
    createObject("", "", `Основы предпринимательской деятельности (с 20.02)
Хильман Л.В.`, "л", "https://edummf.bsu.by/course/view.php?id=952"),
    ],
    "11:15–12:35": [
    createObject("", "", `Основы предпринимательской деятельности (с 06.03)
Хильман Л.В.`, "п"),
    ],
  },
}

const day_of_week = { "Пон.": 1, "Вторник": 2, "Среда": 3, "Пятница": 5, "Суббота": 6 }

export default {
  name: 'HelloWorld',
  data: () => {
  return {
    data: json,
    week: "",
    group: "а",
    search: "",
    };
  },
  created: function () {
    this.week = this.calc_week();
  },
  methods: {
    calc_week: function () {
      let startDate = new Date("31 August 2020, 00:00:00");
      let difference = ((new Date()).getTime() - startDate.getTime());
      let day = Math.floor(difference / (1000 * 60 * 60 * 24)) % 14;
      if (day < 7)
        return "2н";
      else
        return "1н";
    },
    calc: function (objs) {
      let len = objs.length;
      for (let i = 0; i < objs.length; i++) {
        if (objs[i].group == this.oposite(this.group) || objs[i].week == this.oposite(this.week)) {
          len--;
        }
      }
      return len;
    },
    nested_len: function (objs) {
      let len = 0;
      for (let key in objs) {
        len += this.calc(objs[key]);
      }
      return len;
    },
    is_seen: function (obj) {
      return (obj.group == this.group || obj.group == "") && (obj.week == "" || obj.week == this.week);
    },
    is_seen_day: function (objs, obj) {
      for (let key in objs) {
        for (let i = 0; i < objs[key].length; i++) {
          if (this.is_seen(objs[key][i])) {
            return objs[key][i] == obj;
          }
        }
      }
    },
    is_seen_time: function (objs, obj) {
      for (let i = 0; i < objs.length; i++) {
        if (this.is_seen(objs[i])) {
          return objs[i] == obj;
        }
      }
    },
    oposite: function (param) {
      if (param == "")
        return true;
      if (param.includes("1н"))
        return "2н";
      if (param.includes("2н"))
        return "1н";
      if (param.includes("а"))
        return "б";
      if (param.includes("б"))
        return "а";
    },
    is_searched: function (param) {
      if (this.search == "") return false;
      return (param.toString().toLowerCase().includes(this.search.toLowerCase()));
    },
    is_current: function (time, day) {
      let today = new Date();
      if (day_of_week[day] != today.getDay())
        return false;
      var today_str = today.toLocaleDateString('en-GB', {
        day: 'numeric',
        month: 'short',
        year: 'numeric'
      })
      let first_date = new Date(today_str + ", " + time.substring(0, 5).replace(".", ":")).getTime();
      let sec_date = new Date(today_str + ", " + time.substring(6).replace(".", ":")).getTime();
      return today.getTime() >= first_date && today.getTime() <= sec_date;
    },
    is_link: function (item) {
      return item[0].includes("http");
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
body {
  font-family: sans-serif;
  font-size: 14px;
  line-height: 1.6;
  background-color: white;
 }

body > *:first-child {
  margin-top: 0 !important; }
body > *:last-child {
  margin-bottom: 0 !important; }

a {
  color: #4183C4; }
a.absent {
  color: #cc0000; }
a.anchor {
  display: block;
  padding-left: 30px;
  margin-left: -30px;
  cursor: pointer;
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0; }

h1, h2, h3, h4, h5, h6 {
  margin: 20px 0 10px;
  padding: 0;
  font-weight: bold;
  -webkit-font-smoothing: antialiased;
  cursor: text;
  position: relative; }

h1 tt, h1 code {
  font-size: inherit; }

h2 tt, h2 code {
  font-size: inherit; }

h3 tt, h3 code {
  font-size: inherit; }

h4 tt, h4 code {
  font-size: inherit; }

h5 tt, h5 code {
  font-size: inherit; }

h6 tt, h6 code {
  font-size: inherit; }

h1 {
  font-size: 28px;
  color: black; }

h2 {
  font-size: 24px;
  border-bottom: 1px solid #cccccc;
  color: black; }

h3 {
  font-size: 18px; }

h4 {
  font-size: 16px; }

h5 {
  font-size: 14px; }

h6 {
  color: #777777;
  font-size: 14px; }

p, blockquote, ul, ol, dl, li, table, pre {
  margin: 15px 0; }

hr {
  border: 0 none;
  color: #cccccc;
  height: 4px;
  padding: 0; }

body > h2:first-child {
  margin-top: 0;
  padding-top: 0; }
body > h1:first-child {
  margin-top: 0;
  padding-top: 0; }
  body > h1:first-child + h2 {
    margin-top: 0;
    padding-top: 0; }
body > h3:first-child, body > h4:first-child, body > h5:first-child, body > h6:first-child {
  margin-top: 0;
  padding-top: 0; }

a:first-child h1, a:first-child h2, a:first-child h3, a:first-child h4, a:first-child h5, a:first-child h6 {
  margin-top: 0;
  padding-top: 0; }

h1 p, h2 p, h3 p, h4 p, h5 p, h6 p {
  margin-top: 0; }

li p.first {
  display: inline-block; }

ul, ol {
  padding-left: 30px; }

ul :first-child, ol :first-child {
  margin-top: 0; }

ul :last-child, ol :last-child {
  margin-bottom: 0; }

dl {
  padding: 0; }
  dl dt {
    font-size: 14px;
    font-weight: bold;
    font-style: italic;
    padding: 0;
    margin: 15px 0 5px; }
    dl dt:first-child {
      padding: 0; }
    dl dt > :first-child {
      margin-top: 0; }
    dl dt > :last-child {
      margin-bottom: 0; }
  dl dd {
    margin: 0 0 15px;
    padding: 0 15px; }
    dl dd > :first-child {
      margin-top: 0; }
    dl dd > :last-child {
      margin-bottom: 0; }

blockquote {
  border-left: 4px solid #dddddd;
  padding: 0 15px;
  color: #777777; }
  blockquote > :first-child {
    margin-top: 0; }
  blockquote > :last-child {
    margin-bottom: 0; }

table {
  padding: 0; }
  table tr {
    border-top: 1px solid #cccccc;
    background-color: white;
    margin: 0;
    padding: 0; }
    table tr:nth-child(2n) {
      background-color: #f8f8f8; }
    table tr th {
      font-weight: bold;
      border: 1px solid #cccccc;
      text-align: left;
      margin: 0;
      }
    table tr td {
      border: 1px solid #cccccc;
      text-align: left;
      margin: 0;
      }
    table tr th :first-child, table tr td :first-child {
      margin-top: 0; }
    table tr th :last-child, table tr td :last-child {
      margin-bottom: 0; }

table{
  border-collapse: collapse;
  margin-left: 100px;
}

img {
  max-width: 100%; }

span.frame {
  display: block;
  overflow: hidden; }
  span.frame > span {
    border: 1px solid #dddddd;
    display: block;
    float: left;
    overflow: hidden;
    margin: 13px 0 0;
    padding: 7px;
    width: auto; }
  span.frame span img {
    display: block;
    float: left; }
  span.frame span span {
    clear: both;
    color: #333333;
    display: block;
    padding: 5px 0 0; }
span.align-center {
  display: block;
  overflow: hidden;
  clear: both; }
  span.align-center > span {
    display: block;
    overflow: hidden;
    margin: 13px auto 0;
    text-align: center; }
  span.align-center span img {
    margin: 0 auto;
    text-align: center; }
span.align-right {
  display: block;
  overflow: hidden;
  clear: both; }
  span.align-right > span {
    display: block;
    overflow: hidden;
    margin: 13px 0 0;
    text-align: right; }
  span.align-right span img {
    margin: 0;
    text-align: right; }
span.float-left {
  display: block;
  margin-right: 13px;
  overflow: hidden;
  float: left; }
  span.float-left span {
    margin: 13px 0 0; }
span.float-right {
  display: block;
  margin-left: 13px;
  overflow: hidden;
  float: right; }
  span.float-right > span {
    display: block;
    overflow: hidden;
    margin: 13px auto 0;
    text-align: right; }

code, tt {
  margin: 0 2px;
  padding: 0 5px;
  white-space: nowrap;
  border: 1px solid #eaeaea;
  background-color: #f8f8f8;
  border-radius: 3px; }

pre code {
  margin: 0;
  padding: 0;
  white-space: pre;
  border: none;
  background: transparent; }

.highlight pre {
  background-color: #f8f8f8;
  border: 1px solid #cccccc;
  font-size: 13px;
  line-height: 19px;
  overflow: auto;
  border-radius: 3px; }

pre {
  background-color: #f8f8f8;
  border: 1px solid #cccccc;
  font-size: 13px;
  line-height: 19px;
  overflow: auto;
  border-radius: 3px; }
  pre code, pre tt {
    background-color: transparent;
    border: none; }

.current td:not(.active){
  background-color: rgb(251, 253, 143);
}

.active {
  background-color: rgb(121, 121, 121);
}
button{
  margin-right: 30px;
}
#timetable{
  margin-left: 5px;
  margin-right: 15px;
}
li{
  margin: 0px;
}
a {
        text-decoration: none;
  }
*{
  font-family: sans-serif;
}
td{
  padding: 6px 13px; 
}
@media only screen and (max-width: 600px) {
  td, th{
    padding: 2px 4px;
    font-size: 10px;
  }
  .subject{
  width: 150px;
  }
}

</style>
