<template>
  <div class="vhdl-generator content container">

    <div class="columns">

      <div class="column is-5">
        <h3>Upload a CSV</h3>
        <div class="file has-name">
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
        <hr>
        <h3>Paste from table</h3>
        <div class="input-values-textarea">
          <textarea v-model="textareaInput" class="textarea" rows="8" placeholder="Paste text from input table"></textarea>
          <hr>
          <label>Delay:</label>
          <input type="number" min="1" class="input additional-input" v-model="delay">
          <br><br>
          <label>Prefix:</label>
          <input type="text" class="input additional-input" v-model="portPrefix" placeholder="SOURCE">
          <br>
          <hr>

          <button class="button is-primary" style="margin-right: 15px" :disabled="!file" @click="parseCSV">Convert CSV</button>
          <button class="button is-info" :disabled="textareaInput.length < 1" @click="parseTextArea">Convert Text</button>
        </div>
      </div>

      <div class="column is-6 is-offset-1">
        <div v-if="sourceCode.length > 0" class="code">
          <h2>Generated Code</h2>
          <hr>
          <p v-html="sourceCode"></p>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import Papa from 'papaparse';

export default {
  name: 'VHDLGenerator',
  data() {
    return {
      sourceCode: '',
      textareaInput: '',
      portPrefix: '',
      file: null,
      delay: 100
    };
  },
  methods: {
    loadCSV() {
      this.file = document.getElementById('fileItem').files[0];
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
      if (this.textareaInput.length > 0) {
        Papa.parse(this.textareaInput, {
          complete: results => {
            // removes the parsed blank lines
            let data = results.data.filter(el => {
              return el[0].length > 0;
            });

            // splits string of imput values into an array
            for (let i = 0; i < data.length; i++) {
              data[i] = data[i][0].trim().split(' ');
            }

            // puts port name into the beginning of the corresponding array of inputs
            for (let i = 0; i < data.length - 1; i++) {
              if (i % 2 == 0) {
                let portName = data[i][0];
                data[i + 1].unshift(portName);
              }
            }

            // removes the arrays with only the port name
            data = data.filter(el => {
              return el.length > 1;
            });

            this.sourceCode = this.getSourceCode(data);
          }
        });
      }
    },
    getSourceCode(data) {
      let sourceCode = '';
      let sourcePrefix = this.addPrefix ? 'SOURCE_' : '';
      let count = 1;
      while (count < data[0].length) {
        for (let i = 0; i < data.length; i++) {
          sourceCode += `${this.portPrefix}${data[i][0]} <= '${data[i][count]}';`;
          if (i != data.length - 1) {
            sourceCode += ' ';
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
  font-size: 0.8rem;
}

label {
  border-radius: 4px;
  vertical-align: middle;
}

textarea {
  font-size: 0.7rem;
}

.additional-input {
  width: 6em;
  margin-left: 15px;
  vertical-align: middle;
  font-size: 0.8rem;
}
</style>