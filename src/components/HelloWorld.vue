<template>
  <div id="app">
    <header>
      <h2>ТОКЕНИЗАТОР</h2>
    </header>
    <div class="form">
      <p>Выберите, как вы хотите разделить текст:</p>
      <p class="form-title">Умная токенизация</p>
      <p class="form-text">
        Данные 3 вида токенизации предусматривают не только простое разбиение по опредленному
        разделителю (например, по пробелу или по точкам), но и обрабатывает сложные случаи.
        Если не хотите, чтобы программа удаляла дублирующие пробелы, снимите голочку с соответсвующей настройки ниже.
        Если хотите, чтобы был удален последний незначимый символ в тексте (пробельный  символ или перевод строки),
        поставьте галочку в соответсвующей настройке ниже.
        Если хотите другое разбиение на токены, воспользуйтесь разделом "Пользовательская токенизация".
      </p>
      <div class="form__button-default">
        <button
          class="form__button"
          @click="
                tokenType = 'default';
                tokenValue = 'word';"
          v-bind:style="tokenValue === 'word' ? 'background-color: #1c867e' : 'background-color: lightseagreen'">
          На слова
        </button>
        <button
          class="form__button"
          @click="
                tokenType = 'default';
                tokenValue = 'sentence';"
          v-bind:style="tokenValue === 'sentence' ? 'background-color: #1c867e' : 'background-color: lightseagreen'">
          На предложения
        </button>
        <button
          class="form__button"
          @click="tokenType = 'default';
                tokenValue = 'line'"
          v-bind:style="tokenValue === 'line' ? 'background-color: #1c867e' : 'background-color: lightseagreen'">
          На строки
        </button>
      </div>
      <div class="checkboxOverride">
        <input type="checkbox" id="checkboxInputOverride" :value="deleteSpaces" v-model="deleteSpaces">
        <label for="checkboxInputOverride">Убрать дублирующие пробелы</label>
      </div>
      <div class="checkboxOverride">
        <input type="checkbox" id="checkboxInputDelete" v-model="deleteLastSymbol">
        <label for="checkboxInputDelete">Убрать последний незначимый символ в тексте</label>
      </div>
      <div class="form__buttons-custom">
        <p
          v-if="!isCustomTokenType"
          class="button-change-type"
          @click="isCustomTokenType = true">
          <u>Пользовательская токенизация</u>
        </p>
        <div
          v-if="isCustomTokenType">
          <div class="custom-box">
            <button
              class="form__button-custom"
              @click="tokenType = 'custom';
                      tokenValue = 'space'"
              v-bind:style="tokenValue === 'space' ? 'background-color: #1c867e' : 'background-color: lightseagreen'">
              Разделить только по пробелам
            </button>
            <button
              class="form__button-custom"
              @click="tokenType = 'custom';
                      tokenValue = 'punctuation'"
              v-bind:style="tokenValue === 'punctuation' ? 'background-color: #1c867e' : 'background-color: lightseagreen'">
              Разделить по знакам препинания и пробелам
            </button>
            <button
              class="form__button-custom"
              @click="tokenType = 'custom'; tokenValue = 'symbol'"
              v-bind:style="tokenValue === 'symbol' ? 'background-color: #1c867e' : 'background-color: lightseagreen'">
              Разделить посимвольно
            </button>
          </div>
        </div>
      </div>
      <p
        v-if="isCustomTokenType"
        class="button-change-type"
        @click="isCustomTokenType = false">
        <u>Скрыть</u>
      </p>
      <textarea
        v-if="tokenType"
        class="form_textarea"
        style="white-space: pre-wrap;"
        @change="inputText = $event.target.value">
      </textarea>
      <button
        v-if="tokenType"
        class="form__button"
        @click="startTokenization">Разбить на токены
      </button>
      <div v-if="tokensArray.length">
        <p class="tokens-array__title">Результат:</p>
        <p
          class="form_textarea">
          {{ tokensArray }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>

  export default {
    name: 'app',
    components: {
    },
    data() {
      return {
        isCustomTokenType: false,
        tokenType: "",
        tokenValue: "",
        inputText: "",
        tokensArray: [],
        deleteSpaces: true,
        deleteLastSymbol: false,
      }
    },
    watch: {
      deleteSpaces: (v) => {
        console.log(v);
      },
      deleteLastSymbol: (v) => {
        console.log(v);
      }
    },
    methods: {
      deleteSpacesInText() {
        this.inputText = this.inputText.replace(/[ \t\v\r\f]+/g, " ");
      },
      deleteNewLinesInText() {
        this.inputText = this.inputText.replace(/\n+/g, "");
      },
      deleteLastSymbolInText() {
        console.log((this.tokensArray[this.tokensArray.length - 1].replace(/[ \t\v\r\f]+/g, "")));
        if (/\s/.test(this.tokensArray[this.tokensArray.length - 1])) {
          this.tokensArray = this.tokensArray.splice(-1,1);
        }
        console.log(this.tokensArray);
      },
      startTokenization() {
        let type = this.tokenType;
        let value = this.tokenValue;
        if (this.deleteSpaces) {
          this.deleteSpacesInText();
        }
        if (type === 'default') {
          if (value === 'word') {
            this.parseDefaultByWord();
          } else if (value === 'sentence') {
            this.parseDefaultBySentences();
          } else if (value === 'line') {
            this.parseDefaultByLine();
          }
        } else if (type === 'custom') {
          if (value === 'space') {
            this.parseCustomBySpace();
          } else if (value === 'punctuation') {
            this.parseCustomByPunctuation();
          } else if (value === 'symbol') {
            this.parseCustomBySymbol();
          }
        }
        if (this.deleteLastSymbol) {
          this.deleteLastSymbolInText();
        }
      },
      parseDefaultByWord() {
        this.deleteNewLinesInText();
        //this.tokensArray = this.inputText.split(' ');
      },
      parseDefaultBySentences() {
        this.deleteNewLinesInText();
        //this.tokensArray = this.inputText.split(' ');
      },
      parseDefaultByLine() {
        this.tokensArray = this.inputText.split('\n');
      },
      parseCustomBySpace() {
        this.deleteNewLinesInText();
        // this.inputText =  this.inputText.replace(/\"/g, '0').replace(/\\(.)/g, "$1");
        this.tokensArray = this.inputText.split(' ');
      },
      parseCustomByPunctuation() {
        let reg = /[|!|;|:|,|\.|\?|-|\n]/g;
        this.tokensArray = this.inputText.split(reg);
      },
      parseCustomBySymbol() {
        this.deleteNewLinesInText();
        this.tokensArray = this.inputText.split('');
      }
    }
  }
</script>

<style>
  h2 {
    font-family: "Andale Mono";
    font-size: 40px;
    color: lightseagreen;
  }

  header {
    text-align: center;
  }

  .form-title {
    text-align: center;
    font-size: 26px;
    color: lightseagreen;
    margin: 0;
  }

  .form-text {
    max-width: 650px;
    text-align: justify;
    font-size: 16px;
  }

  .form {
    display: flex;
    align-items: center;
    flex-direction: column;
  }

  .form__buttons-default,
  .form__buttons-custom {
    display: flex;
    flex-direction: row;
  }

  .form__button,
  .button-change-type,
  .form_textarea,
  .form__button-custom {
    outline: none;
  }

  .form__button {
    padding: 7px;
    margin: 10px;
    font-size: 15px;
    background-color: lightseagreen;
    color: white;
    width: 180px;

    border-radius: 7px 7px 7px 7px;
    -moz-border-radius: 7px 7px 7px 7px;
    -webkit-border-radius: 7px 7px 7px 7px;
    border: 4px solid gold;

    cursor: pointer;
  }

  .form__button:hover,
  .form__button-custom:hover {
    background-color: #1c867e;
  }

  .button-change-type:hover{
    cursor: pointer;
  }

  .checkboxOverride {
    display: flex;
    flex-direction: row;
    margin: 10px;
  }

  .form_textarea {
    height: 300px;
    width: 600px;
    overflow: scroll;
    padding: 10px;

    border-radius: 7px 7px 7px 7px;
    -moz-border-radius: 7px 7px 7px 7px;
    -webkit-border-radius: 7px 7px 7px 7px;
    border: 4px solid lightseagreen;
  }

  .tokens-array__title {
    text-align: center;
  }

  .custom-box {
    display: flex;
    flex-direction: row;
  }

  .form__button-custom {
    padding: 7px;
    margin: 10px;
    font-size: 12px;
    background-color: lightseagreen;
    color: white;
    width: 180px;

    border-radius: 7px 7px 7px 7px;
    -moz-border-radius: 7px 7px 7px 7px;
    -webkit-border-radius: 7px 7px 7px 7px;
    border: 4px solid gold;

    cursor: pointer;
  }

</style>
