<!-- eslint-disable prettier/prettier -->
<template>
  <div class="container">
    <div class="column inputs">
      <!-- Company selection -->
      <div class="company">
        <label for="company">Selskap</label>
        <select
          id="company"
          class="dropdown"
          v-model="company"
          @change="companyChanged"
        >
          <option value="" disabled>Velg et selskap</option>
          <option v-for="company in companies" :key="company" :value="company">
            {{ company }}
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
    <div class="column uploads">
      <div class="subuploads" v-if="showUploads()">
        <!-- import payment -->
        <p>Importere visma lønn</p>
        <div class="visma-lønn-container">
          <div class="custom-file-upload">
              <label class="visma-lønn-label" for="visma-lønn"><div class="clickable-area">{{ buttonText }}</div></label>
              <input type="file" id="visma-lønn" name="VismaLønn" @change="findFile()"/>
          </div>
          <p class="filename">{{ filename }}</p>
          <input class="submit-button" type="submit">
        </div>
          <!-- distribute coverage contributions -->
        <p>Fordele dekningsbidrag</p>
        <button class="fordel-dekningsbidrag">Fordel</button>
        <!-- import invoice -->
        <form action="/action_page.php">
          <p>Importere fakturagrunnlag</p>
          <div class="invoice-container">  
            <div class="custom-file-upload">
                <label class="invoice-label" for="fakturagrunnlag"><div class="clickable-area">{{ buttonText }}</div></label>
                <input type="file" id="fakturagrunnlag" name="Fakturagrunnlag" @change="findFile()"/> 
            </div>
            <p class="filename" >{{ filename }}</p>
            <input class="submit-button" type="submit">
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
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      company: null,
      companies: ["Kims", "Lays", "Walkers"],
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
      buttonText: 'Velg fil',
      filename: 'Ingen fil valgt',
      log: [],
    };
  },
  methods: {
    companyChanged() {
      console.log(this.company);
    },
    findFile() {
      console.log("finding file name")
      const fileInput = document.querySelector('#visma-lønn');
      const path = fileInput.value
      this.filename = path.split(/(\\|\/)/g).pop();
      console.log(this.filename)
    }
    showUploads() {
      // check if all three inputs are not null
      if (this.company && this.year && this.month) {
        return true;
      }
    },
    sendLønn() {
      var string = `${this.company}:${this.month}:${this.year}:innsending:sendt inn lønn`;
      this.log.push(string);
    },
    sendFaktura() {
      var string = `${this.company}:${this.month}:${this.year}:innsending:sendt inn faktura`;
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
  },
};
</script>
