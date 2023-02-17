<!-- eslint-disable prettier/prettier -->
<template>
  <div class="select-user" v-if="selectedUser == null && login == false">
    <!--select user option from data-->
    <label for="email" class="form-label" @click="selectedUser = 'admin'">Logg inn</label>
    <input type="email" class="input-area small-input" id="email" v-model="userMail" placeholder="Skriv inn epost">
    <input type="password" class="input-area small-input" id="password" v-model="userPassword" placeholder="Skriv inn passord">
    <button @click="verifyLogin()" class="submit-button-small">Logg inn</button>
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
      <h1 class="log-title"><span class="hidden">Hidden content</span><br>Logg</h1>
      <div class="log-window" ref="logContainer">
        <p class="log-entry" v-for="log in log" :key="log">{{ log }}</p>
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
      userMail: null,
      userPassword: null,
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
  updated() {
    this.$nextTick(() => {
      this.$refs.logContainer.scrollTop = this.$refs.logContainer.scrollHeight;
    });
  },
  methods: {
    findFile() {
      console.log("finding file name");
      const fileInput = document.querySelector("#visma-lønn");
      const path = fileInput.value;
      this.filename = path.split(/(\\|\/)/g).pop();
      //shorten filename to max 10 characters
      if (this.filename.length > 10) {
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
        if (this.filename2.length > 10) {
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
    // log user in
    verifyLogin() {
      // send api request using fetch
      fetch("http://localhost:5000/login", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          email: this.userMail,
          password: this.userPassword,
        }),
      })
        .then((response) => response.json())
        .then((data) => {
          console.log(data);
          // check if user exists
          if (data.user) {
            // check if user is admin
            if (data.user.admin == true) {
              this.login = true;
              this.log.push("Innlogging:admin logget inn");
            } else {
              this.login = true;
              this.log.push("Innlogging:bruker logget inn");
            }
          } else {
            alert("Feil brukernavn eller passord");
          }
        });
    },
    // log user out
    logOut() {
      this.login = false;
      this.userMail = null;
      this.userPassword = null;
      this.log.push("Utloggning:bruker logget ut");
    },
  },
};
</script>
