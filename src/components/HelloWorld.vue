<template>
  <div id="app">
    <header>
      <h2>ТОКЕНИЗАТОР</h2>
    </header>
    <div class="form">
      <p>Выберите, как вы хотите разделить текст:</p>
      <div class="form__button-default">
        <button
          class="form__button"
          @click="
                tokenType = 'default';
                tokenValue = 'word';">
          На слова
        </button>
        <button
          class="form__button"
          @click="
                tokenType = 'default';
                tokenValue = 'sentence';">
          На предложения
        </button>
        <button
          class="form__button"
          @click="tokenType = 'default';
                tokenValue = 'line'">
          На строки
        </button>
      </div>
      <div class="form__buttons-custom">
        <p
          v-if="!isCustomTokenType"
          class="button-change-type"
          @click="isCustomTokenType = true">
          <u>Расширенные настройки</u>
        </p>
        <button
          class="form__button"
          v-if="isCustomTokenType"
          @click="tokenType = 'custom'; tokenValue = 'space'">
          По пробелам
        </button>
        <button
          class="form__button"
          v-if="isCustomTokenType"
          @click="tokenType = 'custom'; tokenValue = 'symbol'">
          По знакам препинания и пробелам
        </button>
      </div>
      <p
        v-if="isCustomTokenType"
        class="button-change-type"
        @click="isCustomTokenType = false">
        <u>Скрыть</u>
      </p>
      <textarea v-if="tokenType" class="form_textarea" @change="inputText = $event.target.value">
      </textarea>
      <button
        v-if="tokenType"
        class="form__button"
        @click="startTokenization">
        Разбить на токены
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
      }
    },
    methods: {
      startTokenization() {
        let type = this.tokenType;
        let value = this.tokenValue;
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
          } else if (value === 'symbol') {
            this.parseCustomBySymbol();
          }
        }
      },
      parseDefaultByWord() {
        //this.tokensArray = this.inputText.split(' ');
      },
      parseDefaultBySentences() {
        //this.tokensArray = this.inputText.split(' ');
      },
      parseDefaultByLine() {
        this.tokensArray = this.inputText.split('\n');
      },
      parseCustomBySpace() {
        this.tokensArray = this.inputText.split(' ');
      },
      parseCustomBySymbol() {
        let reg = ",";
        this.tokensArray = this.inputText.split(reg);
      },
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
  .form_textarea {
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

  .form__button:hover {
    background-color: #1c867e;
  }

  .button-change-type:hover{
    cursor: pointer;
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
</style>
