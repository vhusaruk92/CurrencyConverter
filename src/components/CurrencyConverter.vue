<template>
    <div class="currency-converter">
        <h1>{{ msg }}</h1>
        <div>
          <input type="text"
          v-model="inputAmout"
          v-on:keyup.enter="convert"
          placeholder="Amount (GBP)">
        </div>
        <div class="currency-converter__error" v-show="error">{{errorMessage}}</div>
        <div class="currency-converter__output" v-show="outPut">
          <p>{{outPutMessage}}</p>
          <p>Expires in {{ minutes }} minutes {{ seconds }} seconds</p>
        </div>
        <button v-on:click="convert">Convert</button>
        <div class="currency-converter__error" v-show="rateExpired">
          <p>Oops, the rate expired</p>
        </div>

    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class CurrencyConverter extends Vue {
    @Prop() private msg!: string;

    private inputAmout: string|number = ''

    private error = false

    private errorMessage = ''

    private outPut = false

    private outPutMessage = ''

    private expiresIn = ''

    private timer = 0

    private totalTime = (10 * 60)

    private resetButton = false

    private rateExpired = false

    public convert(): void {
      // check if input is valid
      this.rateExpired = false;
      if (!Number(this.inputAmout)) {
        if (!Number(this.inputAmout === 0)) {
          this.errorMessage = `${this.inputAmout} is not a valid number`;
        } else {
          this.errorMessage = 'the field is required and can not be equal to 0';
        }
        this.error = true;
        this.inputAmout = '';
        this.outPut = false;
        this.outPutMessage = '';
      } else {
        this.error = false;
        Vue.axios.get('https://api.exchangerate-api.com/v4/latest/GBP').then((response) => {
          const sum = (Number(this.inputAmout) * response.data.rates.USD).toFixed(2);
          this.outPut = true;
          this.outPutMessage = `${this.inputAmout} GBP is the equivalent of ${sum} USD`;
          this.resetTimer();
          this.startTimer();
        });
      }
    }

    public startTimer(): void {
      this.timer = setInterval(() => this.countdown(), 1000);
      this.resetButton = true;
    }

    public countdown(): void {
      if (this.totalTime >= 1) {
        this.totalTime -= 1;
      } else {
        this.totalTime = 0;
        this.rateExpired = true;
        this.outPut = false;
      }
    }

    public resetTimer(): void {
      this.totalTime = (10 * 60);
      clearInterval(this.timer);
      this.timer = 0;
      this.resetButton = false;
    }

    padTime = (time: number): number => {
      const num = Number((time < 10 ? '0' : '') + time);
      return num;
    }

    get minutes(): number {
      const minutes = Math.floor(this.totalTime / 60);
      return this.padTime(minutes);
    }

    get seconds(): number {
      const seconds = this.totalTime - ((Number(this.minutes) * 60));
      return this.padTime(seconds);
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
* {
    box-sizing: border-box;
    font-size: 16px;
}
  $error-color: red;
  .currency-converter__error {
    color: $error-color;
  }
  h1 {
    font-size: 26px;
  }
  button {
    background: #48b348;
    height: 30px;
    color: #fff;
    border: none;
    border-radius: 4px;
      width: 100px;
  }
  input {
    height: 30px;
    padding: 10px;
  }
  .currency-converter__error,
  input,
  button,
  h1 {
    margin-bottom: 20px;
  }
  p {
    margin-top: 0;
  }
</style>
