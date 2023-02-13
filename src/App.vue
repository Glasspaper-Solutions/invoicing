<!-- eslint-disable prettier/prettier -->
<!-- eslint-disable prettier/prettier -->
<!-- eslint-disable prettier/prettier -->
<template>
  <div class="container">
    <div class="column inputs">
      <!-- Company selection -->
      <div class="company">
        <label for="company">Company</label>
        <select
          id="company"
          v-model="company"
          @change="companyChanged"
          :disabled="!companies.length"
        >
          <option value="" disabled>Select a company</option>
          <option v-for="company in companies" :key="company" :value="company">
            {{ company }}
          </option>
        </select>
      </div>
      <!-- Year input -->
      <div class="year">
        <label for="year">Year</label>
        <input
          id="year"
          type="number"
          v-model="year"
          placeholder="Enter a year"
        />
      </div>
      <!--month selection-->
      <div class="month">
        <label for="month">Month</label>
        <select
          id="month"
          v-model="month"
          :disabled="!months.length"
        >
          <option value="" disabled>Select a month</option>
          <option v-for="month in months" :key="month" :value="month">
            {{ month }}
          </option>
        </select>
      </div>
    </div>
    <div class="column uploads">
      <p>Importere visma lønn</p>
      <form action="/action_page.php">
        <input type="file" id="visma-lønn" name="VismaLønn">
        <input type="submit">
      </form>
      <p>Fordele dekningsbidrag</p>
      <button>Fordel</button>
      <p>Importere fakturagrunnlag</p>
      <form action="/action_page.php">
        <input type="file" id="fakturagrunnlag" name="Fakturagrunnlag">
        <input type="submit">
      </form>
    </div>
    <div class="column log">
      <!--content-->
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
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ],
      month: null,
    };
  },
  methods: {
    companyChanged() {
      console.log(this.company);
    },
  },
};
</script>
