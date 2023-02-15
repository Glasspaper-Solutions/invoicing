<!-- eslint-disable prettier/prettier -->
<template>
  <div class="select-user" v-if="selectedUser == null && login == false">
    <!--select user option from data-->
    <label for="user">Velg bruker</label>
    <select
      id="user"
      class="dropdown"
      v-model="selectedUser"
      @change="userChanged"
    >
      <option value="" disabled>Velg en bruker</option>
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
      <h1>Regnskap <br>Integrasjon</h1>
      <!-- Company selection -->
      <div class="border">
      <div class="company">
        <label for="company">Selskap</label>
        <select
          id="company"
          class="dropdown"
          v-model="company"
          @change="companyChanged"
        >
          <option value="" disabled>Velg et selskap</option>
          <option v-for="company in selectedUser.companies" :key="company" :value="company" v-bind="company">
            {{ company.name }}
          </option>
        </select>
      </div>
      <!-- Year input -->
      <div class="year">
        <label for="year">År</label>
        <input
          id="year"
          class="input-area"
          v-model="year"
          type="number"
          placeholder="Skriv år"
        />
      </div>
      <!--month selection-->
      <div class="month">
        <label for="month">Måned</label>
        <select
          class="dropdown"
          id="month"
          v-model="month"
        >
          <option value="" disabled>Velg en måned</option>
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
                  <div class="clickable-area">{{ buttonText }}</div>
                </label>
                <input type="file" id="visma-lønn" name="VismaLønn" @change="findFile()" />
            </div>
            <p class="filename">{{ filename }}</p>
            <input class="submit-button" type="submit" value="Send fil" v-if="filename != 'Ingen fil valgt'">
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
                <label class="invoice-label" for="fakturagrunnlag"><div class="clickable-area">{{ buttonText }}</div></label>
                <input type="file" id="fakturagrunnlag" name="Fakturagrunnlag" @change="findFile2()"/> 
            </div>
            <p class="filename" >{{ filename2 }}</p>
            <input class="submit-button" type="submit" value="Send fil" v-if="filename2 != 'Ingen fil valgt'">
          </div>
        </form>
      </div>
    </div>
    <div class="column log">
      <!--log window-->
      <p>Logg</p>
      <div class="log-window">
        <p v-for="log in log" :key="log">{{ log }}</p>
      </div>
      <button @click="downloadLog()" class="download">Last ned logg</button>
    </div>
  </div>
  <div class="settings" v-if="login == true">
    <!--log out and back button-->
    <button @click="selectedUser=null, login = false" class="log-out">Logg ut</button>
    <button @click="login = false" class="back">Tilbake</button>
  </div>
</template>

<script>
import Users from "./assets/data/users";

export default {
  name: "App",
  data() {
    return {
      coverage: false,
      login: false,
      company: null,
      year: null,
      months: [
        "Januar",
        "Februar",
        "Mars",
        "April",
        "Mai",
        "Juni",
        "Juli",
        "August",
        "September",
        "Oktober",
        "November",
        "Desember",
      ],
      month: null,
      buttonText: "Velg fil",
      filename: "Ingen fil valgt",
      filename2: "Ingen fil valgt",
      log: [],
      // load json data from file
      data: Users,
      selectedUser: null,
    };
  },
  methods: {
    findFile() {
      console.log("finding file name");
      const fileInput = document.querySelector("#visma-lønn");
      const path = fileInput.value;
      this.filename = path.split(/(\\|\/)/g).pop();
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
  },
};
</script>
