<template>
  <div class="vhdl-generator content container">
    <div class="file has-name is-inline-block">
      <label class="file-label">
        <input class="file-input" type="file" accept=".csv" id="fileItem" @change="loadCSV">
        <span class="file-cta">
          <span class="file-label">
            Choose a file…
          </span>
        </span>
        <span class="file-name">
          <span v-if="file">{{ file.name }}</span>
          <span v-else>No file selected</span>
        </span>
      </label>
    </div>
    <span>Delay:</span>
    <input type="number" min="1" class="input number-input" v-model="delay">
    <button class="button is-info" :disabled="!file" @click="parseCSV">Get code</button>

    <div v-if="sourceCode.length > 0" class="code">
      <hr>
      <p v-html="sourceCode"></p>
    </div>

  </div>
</template>

<script>
import Papa from "papaparse";

export default {
  name: "VHDLGenerator",
  data() {
    return {
      sourceCode: "",
      file: null,
      delay: 100
    };
  },
  methods: {
    loadCSV() {
      this.file = document.getElementById("fileItem").files[0];
    },
    parseCSV() {
      if (this.file) {
        Papa.parse(this.file, {
          complete: results => {
            this.sourceCode = this.getSourceCode(results.data);
          }
        });
      }
    },
    getSourceCode(data) {
      let sourceCode = "";
      let count = 1;
      while (count < data[0].length) {
        for (let i = 0; i < data.length - 1; i++) {
          sourceCode += `${data[i][0]} <= '${data[i][count]}';`;
          if (i != data.length - 1) {
            sourceCode += " ";
          }
        }
        count++;
        sourceCode += `<br>wait for ${this.delay} ns;<br><br>`;
      }
      return sourceCode;
    }
  }
};
</script>

<style lang="scss" scoped>
.vhdl-generator {
  margin: 35px auto 0;
  text-align: center;
  width: 80%;
}

.file {
  margin-right: 30px;

  label {
    border-radius: 4px;
  }
}

.number-input {
  width: 5em;
  margin-left: 15px;
  margin-right: 35px;
}

.code {
  margin-top: 35px;
  margin: 0 auto;
  text-align: left !important;
}

</style>