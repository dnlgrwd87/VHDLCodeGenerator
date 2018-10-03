<template>
  <div class="vhdl-generator content container">

    <div class="columns">

      <div class="column is-5">
        <h3>Upload a file</h3>
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
        <hr><p>OR</p><hr>
        <h3>Paste from table</h3>
        <div class="input-values-textarea">
          <textarea v-model="inputValues" class="textarea" rows="12" placeholder="Paste text from input table"></textarea>
          <hr>
          <label class="checkbox">
            <input style="margin-right: 15px" v-model="addPrefix" type="checkbox">Add "SOURCE_" before port names
          </label>
          <br><br>
          <button class="button is-primary" style="margin-right: 15px" :disabled="!file" @click="parseCSV">Convert Uploaded CSV</button>
          <button class="button is-info" :disabled="inputValues.length < 1" @click="parseTextArea">Convert Text</button>
        </div>
      </div>

      <div class="column is-7">
        <div v-if="sourceCode.length > 0" class="code">
          <h2>Generated Code</h2>
          <p v-html="sourceCode"></p>
        </div>
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
      inputValues: "",
      addPrefix: false,
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
            results.data.pop();

            this.sourceCode = this.getSourceCode(results.data);
          }
        });
      }
    },
    parseTextArea() {
      if (this.inputValues.length > 0) {
        Papa.parse(this.inputValues.trim(), {
          complete: results => {
            let data = results.data;
            for (let i = 0; i < data.length; i++) {
              data[i] = data[i][0].trim().split(" ");
            }

            for (let i = 0; i < data.length - 1; i++) {
              if (i % 2 == 0) {
                let portName = data[i][0];
                data[i + 1].unshift(portName);
              }
            }

            data = data.filter(el => {
              return el.length > 1;
            });
            this.sourceCode = this.getSourceCode(data);
          }
        });
      }
    },
    getSourceCode(data) {
      let sourceCode = "";
      let sourcePrefix = this.addPrefix ? "SOURCE_" : "";
      let count = 1;
      while (count < data[0].length) {
        for (let i = 0; i < data.length; i++) {
          sourceCode += `${sourcePrefix}${data[i][0]} <= '${data[i][count]}';`;
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
  margin-top: 25px;
  width: 80%;
}

.file {
  margin-right: 30px;

  label {
    border-radius: 4px;
  }
}

textarea {
  margin-top: 25px;
  font-size: 0.8rem;
}

.number-input {
  width: 5em;
  margin-left: 15px;
}

.code {
  margin-left: 50px;
  text-align: left !important;
}
</style>