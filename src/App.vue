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
        <p>Importere visma lønn</p>
        <form action="/action_page.php" v-on:submit="sendLønn()">
          <input type="file" id="visma-lønn" name="VismaLønn">
          <input type="submit">
        </form>
        <p>Fordele dekningsbidrag</p>
        <button>Fordel</button>
        <p>Importere fakturagrunnlag</p>
        <form action="/action_page.php" v-on:submit="sendFaktura()">
          <input type="file" id="fakturagrunnlag" name="Fakturagrunnlag">
          <input type="submit">
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
      log: [],
    };
  },
  methods: {
    companyChanged() {
      console.log(this.company);
    },
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
      var element = document.createElement("a");
      var file = new Blob([this.log], { type: "text/plain" });
      element.href = URL.createObjectURL(file);
      element.download = "logg.txt";
      element.click();
    },
  },
};
</script>
