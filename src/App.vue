<!-- eslint-disable prettier/prettier -->
<template>
  <div class="select-user" v-if="selectedUser == null && login == false">
    <!--select user option from data-->
    <label for="user">{{ lang.chooseUser }}</label>
    <select
      id="user"
      class="dropdown"
      v-model="selectedUser"
      @change="userChanged"
    >
      <option value="" disabled>{{ lang.chooseAUser }}</option>
      <option v-for="user in data.users" :key="user" :value="user">
        {{ user.name }}
      </option>
    </select>
  </div>
  <div @click="login=true" v-if="selectedUser != null">
    <img class="settings" src="./assets/settings-icon.svg" alt="innstillinger">
  </div>
  <div class="container" v-if="selectedUser != null && login == false">
    <div class="column inputs">
      <h1>{{ lang.accounting }} <br>{{ lang.integration }}</h1>
      <!-- Company selection -->
      <div class="border">
        <div class="company">
          <label for="company">{{ lang.company }}</label>
          <select
            id="company"
            class="dropdown"
            v-model="company"
            @change="companyChanged"
          >
            <option value="" disabled>{{ lang.chooseACompany }}</option>
            <option v-for="company in selectedUser.companies" :key="company" :value="company" v-bind="company">
              {{ company.name }}
            </option>
          </select>
        </div>
        <!-- Year input -->
        <div class="year">
          <label for="year">{{ lang.year }}</label>
          <input
            id="year"
            class="input-area"
            v-model="year"
            type="number"
            :placeholder="lang.writeWhatYear"
          />
        </div>
        <!--month selection-->
        <div class="month">
          <label for="month">{{ lang.month }}</label>
          <select
            class="dropdown"
            id="month"
            v-model="month"
          >
            <option value="" disabled>{{ lang.chooseAMonth }}</option>
            <option v-for="month in months" :key="month" :value="month">
              {{ month }}
            </option>
          </select>
        </div>
      </div>
    </div>
    <div class="column uploads">
      <div class="subuploads" v-if="showUploads()">
        <form action="javascript:void(0);" @submit="sendLønn()">
          <p v-if="showSalary()">Importere visma lønn</p>
          <div class="visma-lønn-container" v-if="showSalary()">
            <div class="custom-file-upload">
                <label class="visma-lønn-label" for="visma-lønn">
                  <div class="clickable-area">{{ lang.chooseFile }}</div>
                </label>
                <input type="file" id="visma-lønn" name="VismaLønn" @change="findFile()" />
            </div>
            <p class="filename">{{ filename }}</p>
            <input class="submit-button" type="submit" :value="lang.sendFile" v-if="filename != 'Ingen fil valgt'">
          </div>
        </form>
        <!-- distribute coverage contributions -->
        <p v-if="showCoverage()" style="margin-top: 90px">Fordele dekningsbidrag</p>
        <input type="checkbox" v-if="showCoverage()" class="fordel-dekningsbidrag" v-model="coverage" v-on:change="logCoverage()">
        <!-- import invoice -->
        <form action="javascript:void(0);" v-if="showInvoice()" @submit="sendFaktura()">
          <p>Importere fakturagrunnlag</p>
          <div class="invoice-container">
            <div class="custom-file-upload">
                <label class="invoice-label" for="fakturagrunnlag"><div class="clickable-area">{{ lang.chooseFile }}</div></label>
                <input type="file" id="fakturagrunnlag" name="Fakturagrunnlag" @change="findFile2()"/> 
            </div>
            <p class="filename" >{{ filename2 }}</p>
            <input class="submit-button" type="submit" :value="lang.sendFile" v-if="filename2 != 'Ingen fil valgt'">
          </div>
        </form>
      </div>
    </div>
    <div class="column log">
      <!--log window-->
      <h1 class="log-title"><span class="hidden">Hidden content</span><br>{{ lang.log }}</h1>
      <div class="log-window" ref="logContainer">
        <p class="log-entry" v-for="log in log" :key="log">{{ log }}</p>
      </div>
      <button @click="downloadLog()" class="download">{{ lang.downloadLog }}</button>
    </div>
  </div>
  <div class="settings" v-if="login == true">
    <!--log out and back button-->
    <button @click="selectedUser=null, login = false" class="log-out">{{ lang.logOut }}</button>
    <button @click="login = false" class="back">{{ lang.back }}</button>
    <!--select language-->
    <div class="language">
      <label for="language" class="language-label">{{ lang.language }}</label>
      <select
        id="language"
        class="dropdown"
        v-model="langName"
        @change="langChanged()"
      >
        <option value="no">Norsk</option>
        <option value="en">English</option>
      </select>
    </div>
  </div>
</template>

<script>
import Users from "./assets/data/users";
import No from "./assets/lang/no";
import En from "./assets/lang/en";

export default {
  name: "App",
  data() {
    return {
      // change langName here to be page default
      langName: "en",
      lang: null,
      coverage: false,
      login: false,
      company: null,
      year: null,
      months: null,
      month: null,
      buttonText: "Velg fil",
      filename: null,
      filename2: null,
      log: [],
      // load json data from file
      data: Users,
      selectedUser: null,
    };
  },
  beforeMount() {
    console.log(this.langName);
    // set lang
    this.lang = this.langName === "no" ? No.words : En.words;
    this.months = [
      this.lang.months.january,
      this.lang.months.february,
      this.lang.months.march,
      this.lang.months.april,
      this.lang.months.may,
      this.lang.months.june,
      this.lang.months.july,
      this.lang.months.august,
      this.lang.months.september,
      this.lang.months.october,
      this.lang.months.november,
      this.lang.months.december,
    ];
    console.log(this.lang);
  },
  updated() {
    this.$nextTick(() => {
      if (this.$refs.logContainer != null) {
        this.$refs.logContainer.scrollTop =
          this.$refs.logContainer.scrollHeight;
      }
    });
  },
  methods: {
    findFile() {
      console.log("finding file name");
      const fileInput = document.querySelector("#visma-lønn");
      const path = fileInput.value;
      this.filename = path.split(/(\\|\/)/g).pop();
      //shorten filename to max 10 characters
      if (this.filename.length > 13) {
        this.filename = this.filename.substring(0, 16) + "...";
      }
      if (this.filename === undefined) {
        this.filename = "No file selected";
      }
      console.log(this.filename);
    },
    findFile2() {
      console.log("finding file name");
      const fileInput = document.querySelector("#fakturagrunnlag");
      const path = fileInput.value;
      // Check if the input is empty
      if (path) {
        this.filename2 = path.split(/(\\|\/)/g).pop();
        //shorten filename to max 10 characters
        if (this.filename2.length > 13) {
          this.filename2 = this.filename2.substring(0, 16) + "...";
        }
        console.log(this.filename2);
      } else {
        console.log("No file was selected");
      }
    },
    showUploads() {
      // check if all three inputs are not null
      if (this.company && this.year && this.month) {
        return true;
      }
    },
    sendLønn() {
      var string = `${this.company.name}:${this.month}:${this.year}:innsending:sendt inn lønn`;
      this.log.push(string);
    },
    sendFaktura() {
      var string = `${this.company.name}:${this.month}:${this.year}:innsending:sendt inn faktura`;
      this.log.push(string);
    },
    downloadLog() {
      // download log to txt file where every item in the array is separated on a new line
      var string = "";
      for (var i = 0; i < this.log.length; i++) {
        string += this.log[i] + "\n";
      }
      var blob = new Blob([string], { type: "text/plain" });
      var link = document.createElement("a");
      link.href = window.URL.createObjectURL(blob);
      link.download = "logg.txt";
      link.click();
    },
    showInvoice() {
      if (this.company == null) {
        return false;
      }
      // check if "invoice" is in array without using includes()
      for (var i = 0; i < this.company.applications.length; i++) {
        if (this.company.applications[i] == "invoice") {
          return true;
        }
      }
      return false;
    },
    showCoverage() {
      if (this.company == null) {
        return false;
      }
      // check if "coverage" is in array without using includes()
      for (var i = 0; i < this.company.applications.length; i++) {
        if (this.company.applications[i] == "coverage") {
          return true;
        }
      }
      return false;
    },
    showSalary() {
      if (this.company == null) {
        return false;
      }
      // check if "salary" is in array without using includes()
      for (var i = 0; i < this.company.applications.length; i++) {
        if (this.company.applications[i] == "salary") {
          return true;
        }
      }
      return false;
    },
    // log coverage value
    logCoverage() {
      if (this.coverage == true) {
        var string = `${this.company.name}:${this.month}:${this.year}:fordeling:dekningsbidrag fordeles`;
        this.log.push(string);
      } else if (this.coverage == false) {
        var str = `${this.company.name}:${this.month}:${this.year}:fordeling:dekningsbidrag fordeles ikke`;
        this.log.push(str);
      } else {
        console.log("error");
      }
    },
    langChanged() {
      console.log(this.langName);
      // set lang
      this.lang = this.langName === "no" ? No.words : En.words;
      this.months = [
        this.lang.months.january,
        this.lang.months.february,
        this.lang.months.march,
        this.lang.months.april,
        this.lang.months.may,
        this.lang.months.june,
        this.lang.months.july,
        this.lang.months.august,
        this.lang.months.september,
        this.lang.months.october,
        this.lang.months.november,
        this.lang.months.december,
      ];
      console.log(this.lang);
    },
  },
};
</script>
