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
      <!-- import payment -->
      <p>Importere visma lønn</p>
      <div class="custom-file-upload">
          <label class="visma-lønn-label" for="visma-lønn" @click=""><div class="clickable-area">{{ buttonText }}</div></label>
          <input type="file" id="visma-lønn" name="VismaLønn" @change="findFile()" />
      </div>
      <p>{{ filename }}</p>
      <input type="submit">
      <!-- distribute coverage contributions -->
      <p>Fordele dekningsbidrag</p>
      <button>Fordel</button>
      <!-- import invoice -->
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
      buttonText: 'Velg fil',
      filename: null,
    };
  },
  methods: {
    companyChanged() {
      console.log(this.company);
    },
    onFileSelected(event) {
      const file = event.target.files[0];
      // Do something with the selected file
    },
    findFile() {
      console.log("finding file name")
      const fileInput = document.querySelector('#visma-lønn');
      const path = fileInput.value
      this.filename = path.split(/(\\|\/)/g).pop();
      console.log(this.filename)
    }
  },
};
</script>
