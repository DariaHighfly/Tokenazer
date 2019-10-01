<template>
  <div id="app">
    <header>
      <h2>ТОКЕНИЗАТОР</h2>
    </header>
    <div class="form">
      <p class="form-text">Выберите, как вы хотите разделить текст:</p>

      <p class="form-title">Сегментация</p>
      <div class="custom-box">
        <button
          class="form__button"
          @click="
                tokenType = 'default';
                tokenValue = 'sentence';"
          v-bind:style="tokenValue === 'sentence' ? 'background-color: #1c867e' : 'background-color: lightseagreen'">
          На предложения
        </button>
      </div>

      <p class="form-title">Токенизация</p>
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
          @click="tokenType = 'default';
                  tokenValue = 'line'"
          v-bind:style="tokenValue === 'line' ? 'background-color: #1c867e' : 'background-color: lightseagreen'">
          На строки
        </button>
      </div>
      <p
        v-if="!isCustomTokenType"
        class="button-change-type"
        @click="isCustomTokenType = true">
        <u>Пользовательская токенизация</u>
      </p>
      <div class="form__buttons-custom" v-if="isCustomTokenType">
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
            Разделить по знакам препинания,переносу строк и пробелам
          </button>
          <button
            class="form__button-custom"
            @click="tokenType = 'custom'; tokenValue = 'symbol'"
            v-bind:style="tokenValue === 'symbol' ? 'background-color: #1c867e' : 'background-color: lightseagreen'">
            Разделить посимвольно
          </button>
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
    methods: {
      deleteSpacesInText() {
        this.inputText = this.inputText.replace(/[ \t\v\r\f]+/g, " ");
        while (this.inputText[this.inputText.length - 1] === " " && this.inputText.length !== 0) {
          this.inputText = this.inputText.substring(0, this.inputText.length - 1);
        }
      },
      deleteNewLinesInText() {
        this.inputText = this.inputText.replace(/\n+/g, "");
      },
      deleteLastSymbolInText() {
        let test = this.tokensArray[this.tokensArray.length - 1].replace(/[ \t\v\r\f\n\s]+/g, "");
        if (test === "") {
          this.tokensArray.splice(-1,1);
        }
      },
      replaceQuotes() {
        this.inputText = this.inputText.replace(/\"/g, "'");
      },
      startTokenization() {
        let type = this.tokenType;
        let value = this.tokenValue;
        this.replaceQuotes();
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
        this.deleteLastSymbolInText();
      },
      parseDefaultByWord() {
        this.deleteNewLinesInText();
        this.tokensArray = this.inputText.split(' ');
      },
      parseDefaultBySentences() {
        this.tokensArray = [];
        this.deleteSpacesInText();
        this.deleteNewLinesInText();
        let i = 0;
        let quotesBegins = false;
        let punctuationBegins = false;
        while (i < this.inputText.length) {
          if (this.inputText[i].match(/[!?]/)) {
            if (!punctuationBegins) {
              punctuationBegins = true;
            }
            if (this.tokensArray.length === 0) {
              this.tokensArray = ["Введенный текст некорректен относительно правил русского языка"];
              break;
            }
            if (this.inputText[i] === "!" && this.inputText[i - 1].match(/[0-9]/)) {
              punctuationBegins = false;
            }
            // могут  быть несколько знаков вопроса и восклицания
            this.tokensArray[this.tokensArray.length - 1] = this.tokensArray[this.tokensArray.length - 1] + this.inputText[i];
            ++i;
          } else if (this.inputText[i].match(/[.]/)) {
            if (this.tokensArray.length === 0) {
              this.tokensArray = ["Введенный текст некорректен относительно правил русского языка"];
              break;
            }
            // может быть многоточие
            if (i + 2 < this.inputText.length &&
              (this.inputText[i + 1].match(/[.]/)) && (this.inputText[i + 2].match(/[.]/))) {
              this.tokensArray[this.tokensArray.length - 1] = this.tokensArray[this.tokensArray.length - 1] + "...";
              i += 3;
              punctuationBegins = true;
            } else if (this.inputText[i - 1].match(/[0-9]/) && this.inputText[i + 1].match(/[0-9]/)) {
              this.tokensArray[this.tokensArray.length - 1] = this.tokensArray[this.tokensArray.length - 1] + ".";
              punctuationBegins = false;
              ++i;
              // или аббривиатура, или инициал
            } else if (i - 1 >= 0 && this.inputText[i - 1].match(/[А-ЯЁ]/)) {
              this.tokensArray[this.tokensArray.length - 1] =
                this.tokensArray[this.tokensArray.length - 1] + ".";
              punctuationBegins = false;
              ++i;
              // или сокращения
            } else if (i - 1 >= 0 && i + 2 < this.inputText.length &&
              (this.inputText.substring(i - 1, i + 3) === "т.е.")) {
              this.tokensArray[this.tokensArray.length - 1] =
                this.tokensArray[this.tokensArray.length - 1] + this.inputText.substring(i, i + 3);
              punctuationBegins = false;
              i += 3;
            } else if (i - 1 >= 0 && i + 3 < this.inputText.length &&
              (this.inputText.substring(i - 1, i + 4) === "т. е.")) {
              this.tokensArray[this.tokensArray.length - 1] =
                this.tokensArray[this.tokensArray.length - 1] + this.inputText.substring(i, i + 4);
              punctuationBegins = false;
              i += 4;
            } else if (i - 3 >= 0 && i + 2 < this.inputText.length &&
              (this.inputText.substring(i - 3, i + 3) === "и т.п." ||
                this.inputText.substring(i - 3, i + 3) === "и т.д.")) {
              this.tokensArray[this.tokensArray.length - 1] =
                this.tokensArray[this.tokensArray.length - 1] + this.inputText.substring(i, i + 3);
              punctuationBegins = false;
              i += 3;
            } else if (i - 3 >= 0 && i + 3 < this.inputText.length &&
              (this.inputText.substring(i - 3, i + 4) === "и т. п." ||
                this.inputText.substring(i - 3, i + 4) === "и т. д.")) {
              this.tokensArray[this.tokensArray.length - 1] =
                this.tokensArray[this.tokensArray.length - 1] + this.inputText.substring(i, i + 4);
              punctuationBegins = false;
              i += 4;

            } else if (i - 4 >= 0 &&
              (this.inputText.substring(i - 4, i + 1) === "и др." ||
                this.inputText.substring(i - 4, i + 1) === "и пр.")) {
              this.tokensArray[this.tokensArray.length - 1] =
                this.tokensArray[this.tokensArray.length - 1] + ".";
              punctuationBegins = false;
              ++i;
            } else {
              // значит, просто знак препинания
              this.tokensArray[this.tokensArray.length - 1] = this.tokensArray[this.tokensArray.length - 1] + ".";
              punctuationBegins = true;
              ++i;
            }
          } else if (this.inputText[i].match(/["«»‘’']/)) {
            if (this.tokensArray.length === 0 || (punctuationBegins === true && quotesBegins === false)) {
              this.tokensArray.push(this.inputText[i]);
            } else {
              this.tokensArray[this.tokensArray.length - 1] = this.tokensArray[this.tokensArray.length - 1] + this.inputText[i];
            }
            if (!quotesBegins) {
              quotesBegins = true;
            } else {
              quotesBegins = false;
              console.log(this.inputText[i + 1]);
              console.log(this.inputText[i + 2]);
              console.log(this.inputText[i + 3]);
              if (i + 3 < this.inputText.length &&
                (this.inputText[i + 1].match(/[–-]+/) ||
                  this.inputText[i + 2].match(/[–-]+/) ||
                  this.inputText[i + 3].match(/[–-]+/))) {
                console.log("*");
                punctuationBegins = false;
              }
            }
            ++i;
          } else {
            if (punctuationBegins && !quotesBegins) {
              punctuationBegins = false;
              if (this.inputText[i] === " ") {
                this.tokensArray.push(this.inputText[i + 1]);
                i += 2;
              } else {
                this.tokensArray.push(this.inputText[i]);
                ++i;
              }
            } else {
              if (this.tokensArray.length === 0) {
                this.tokensArray.push(this.inputText[i]);
                ++i;
              } else {
                this.tokensArray[this.tokensArray.length - 1] = this.tokensArray[this.tokensArray.length - 1] + this.inputText[i];
                ++i;
              }
            }
          }
        }
      },
      parseDefaultByLine() {
        this.tokensArray = this.inputText.split('\n');
      },
      parseCustomBySpace() {
        this.deleteNewLinesInText();
        this.tokensArray = this.inputText.split(' ');
      },
      parseCustomByPunctuation() {
        for (let i = 0; i !== this.inputText.length; ++i) {
          if (this.inputText[i].match(/[!?.:;,]/) &&
            (i !== this.inputText.length - 2) && (this.inputText[i + 1] === " ")) {
            this.inputText = this.inputText.slice(0, i + 1) + this.inputText.slice(i + 2, this.inputText.length);
          }
        }
        let reg = /[|!|;|:|,|\.|\?|-|--|\n\s]+/g;
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
    font-size: 18px;
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
