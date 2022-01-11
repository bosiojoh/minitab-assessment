<template>
  <div class="container">
    <form @submit="validateSize" @reset="resetForm">
      <label for="size">Sample Size:</label>
      <input id="size" v-model.number="size" :disabled="lockForm">
      <br/>
      <label for="mean">Sample Mean:</label>
      <input id="mean" v-model.number="mean" :disabled="lockForm">
      <br/>
      <label for="stdev">Standard Deviation:</label>
      <input id="stdev" v-model.number="stdev" :disabled="lockForm">
      <br/>
      <span class="check-container">
        <input 
          id="hyp-test" 
          type="checkbox" 
          v-model="performTest" 
          :disabled="lockForm"
          @change="clearHypMean">
        <label for="hyp-test">Perform hypothesis test</label>
      </span>
      <br/>
      <label for="hyp-mean" class="indent" :class="{'disabled-text': !performTest }">Hypothesized Mean:</label>
      <input id="hyp-mean" v-model.number="hypMean" :disabled="!performTest || lockForm">

      <span class="button-container">
        <button class="ok-btn" type="submit">OK</button>
        <button class="reset-btn" type="reset">Reset</button>
      </span>
    </form>

    <div v-if="showTable" class="table-container">
      <table>
        <thead>
          <th>Parameter</th>
          <th>Value</th>
        </thead>
        <tr>
          <td>Sample Size</td>
          <td>{{ size }}</td>
        </tr>
        <tr>
          <td>Sample Mean</td>
          <td>{{ mean }}</td>
        </tr>
        <tr>
          <td>Standard Deviation</td>
          <td>{{ stdev }}</td>
        </tr>
        <tr v-if="performTest">
          <td>Hypothesized Mean</td>
          <td>{{ hypMean }}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Form',
  props: {
    msg: String
  },
  data: function () {
    return {
      size: null,
      mean: null,
      stdev: null,
      performTest: false,
      hypMean: null,
      showTable: false,
      lockForm: false
    }
  },
  methods: {
    validateSize(event) {

      let errorFields = []

      if(!this.isNumber(this.size) || this.size < 2 || this.size % 1) {
        errorFields.push('Sample size');
      }

      if(!this.isNumber(this.mean)) {
        errorFields.push('Sample mean');
      }

      if(!this.isNumber(this.stdev) || this.stdev <= 0) {
        errorFields.push('Standard Deviation');
      }

      // Only check this field if enabled
      if(this.performTest && !this.isNumber(this.hypMean)) {
        errorFields.push('Hypothesized Mean');
      }

      // If one or more errors found, show message
      if(errorFields.length) {
        let message = 'Error processing form due to invalid fields:\n'
        message += errorFields.join('\n');
        window.alert(message);
      } else {
        this.showTable = true;
        this.lockForm = true;
      }

      event.preventDefault();
    },
    isNumber(value) {
      return typeof value === 'number';
    },
    resetForm() {
      this.size = null;
      this.mean = null;
      this.stdev = null;
      this.hypMean = null;
      this.performTest = false;
      this.showTable = false;
      this.lockForm = false;
    },
    clearHypMean() {
      this.hypMean = null;
    }
  }
}
</script>

<style scoped>
.container {
  margin: auto;
  width: 500px;
}
form {
  display: grid;
  grid-template-columns: 50% 50%;
}
input {
  grid-column: 2;
  padding: 5px;
}
label {
  grid-column: 1;
  display: inline-block;
}
.check-container {
  grid-column: 1;
}
.indent {
  margin-left: 50px;
}
.button-container {
  grid-column: 2;
  margin-top: 20px;
}
button {
  padding: 10px 0px;
  width: 100px;
  font-weight: bold;
  cursor: pointer;
}
.ok-btn {
  background-color: #1a70ad;
  color: white;
  border-radius: 5px;
  border: none;
}
.reset-btn {
  background-color: white;
  color: gray;
  border-radius: 5px;
  border: 1px solid gray;
  margin-left: 5px;
}
.disabled-text {
  color: lightgray;
}
table {
  width: 100%;
  border: 1px solid black;
  border-collapse: collapse;
  margin-top: 50px;
}
td, th {
  border: 1px solid black;
  text-align: left;
  width: 50%;
  padding: 10px 5px;
}
tr:nth-child(odd){
  background-color: lightgray;
}
</style>