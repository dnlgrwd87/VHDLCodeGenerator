<template>
  <div class="vhdl-generator content">
    <div class="container">

      <div class="file has-name is-pulled-left">
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
      <button class="button is-info" :disabled="!file" @click="parseCSV">Get code</button>

      <div v-if="sourceCode.length > 0" class="code">
        <hr>
        <p v-html="sourceCode"></p>
      </div>

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
      file: null
    };
  },
  methods: {
    loadCSV() {
      this.file = document.getElementById("fileItem").files[0];
      console.log(this.file);
    },
    parseCSV() {
      if (this.file) {
        Papa.parse(this.file, {
          complete: results => {
            this.sourceCode = this.getSourceCode(results.data);
            console.log(this.sourceCode);
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
        sourceCode += `<br>wait for 100 ns;<br><br>`;
      }
      return sourceCode;
    }
  }
};
</script>

<style lang="scss" scoped>

.vhdl-generator {
  margin-top: 35px;
}

.file {
  margin-right: 30px;

  label {
    border-radius: 4px;
  }
}

.code {
  margin-top: 35px;
}
</style>