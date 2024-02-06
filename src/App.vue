<template>
  <body>
    <header>
      <h1>SPLI<br />TTER</h1>
    </header>
    <main class="content">
      <div class="calc-section">
        <label for="bill">Bill</label>
        <input
          type="number"
          id="bill"
          @change="calculateTip()"
          v-model="bill"
          placeholder="0"
          style="background-image: url('/icon-dollar.svg')"
        />
        <label for="tip">Select Tip %</label>
        <div ref="buttonContainer" class="button-container">
          <button aria-selected="false" @click="handleClick" value="5">
            5%</button
          ><button aria-selected="false" @click="handleClick" value="10">
            10%</button
          ><button aria-selected="false" @click="handleClick" value="15">
            15%</button
          ><button aria-selected="false" @click="handleClick" value="25">
            25%</button
          ><button aria-selected="false" @click="handleClick" value="50">
            50%
          </button>
          <input
            type="number"
            id="tip"
            @change="resetTipButtons() + calculateTip()"
            v-model="tip"
            placeholder="Custom"
          />
        </div>
        <label for="people">Number of People </label>
        <span v-if="errors.people" class="errorMassage">{{
          errors.people
        }}</span>
        <input
          type="number"
          id="people"
          v-model="people"
          @change="calculateTip()"
          :class="errors.people ? 'error' : ''"
          placeholder="0"
          style="background-image: url('/icon-person.svg')"
          min="0"
        />
      </div>
      <div class="result-section">
        <div>
          <div class="total-category">
            <div class="total-category-text">
              Tip Amount
              <span><br />/ person</span>
            </div>
            <span>${{ formatPrice(tipAmountPerPerson) }}</span>
          </div>
          <div class="total-category">
            <div class="total-category-text">
              Total
              <span><br />/ person</span>
            </div>
            <span>${{ formatPrice(totalAmountPerPerson) }}</span>
          </div>
        </div>
        <button
          id="resetButton"
          @click="resetAllValues"
          :disabled="buttonDisabled"
        >
          RESET
        </button>
      </div>
    </main>
  </body>
</template>

<script setup>
import { ref } from 'vue';

const bill = ref('');
const tip = ref('');
const people = ref('');
const tipAmountPerPerson = ref(0);
const totalAmountPerPerson = ref(0);
const errors = ref([]);

const buttonContainer = ref(null);

const buttonDisabled = ref(true);

function calculateTip() {
  buttonDisabled.value = true;
  if (validate()) {
    let billPerPerson = bill.value / people.value;
    let tipAmount = bill.value * (tip.value / 100);
    tipAmountPerPerson.value = tipAmount / people.value;
    totalAmountPerPerson.value = billPerPerson + tipAmountPerPerson.value;

    buttonDisabled.value = false;
  }
}

function formatPrice(price) {
  return new Intl.NumberFormat('en-US').format(price);
}

function handleClick(e) {
  tip.value = e.target.value;
  resetTipButtons();
  e.target.setAttribute('aria-selected', 'true');
  calculateTip();
}

function resetTipButtons() {
  let buttonsArray = [...buttonContainer.value.children];

  buttonsArray.forEach((element) => {
    if (element.localName === 'button')
      element.setAttribute('aria-selected', 'false');
  });
}

function validate() {
  errors.value = [];

  if (bill.value !== '' && tip.value !== '' && people.value !== '') {
    if (people.value == 0) {
      errors.value['people'] = 'Number of people cant be zero!';
      return false;
    }
    return true;
  }
  return false;
}

function resetAllValues() {
  bill.value = '';
  tip.value = '';
  people.value = '';
  tipAmountPerPerson.value = 0;
  totalAmountPerPerson.value = 0;
  buttonDisabled.value = true;
}
</script>

<style scoped>
body {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: var(--clr-light-grayish-cyan);
  min-height: 100vh;
  font-weight: 700;
  font-family: 'Space Mono', sans-serif;
}

header h1 {
  color: var(--clr-very-dark-cyan);
  letter-spacing: 0.5em;
  margin-top: 40px;
}

.content {
  background-color: var(--clr-white);
  border-radius: 20px 20px 0 0;
  padding: 1.5rem;
  display: flex;
  gap: 10px;
  flex-direction: column;
  justify-content: space-between;
  margin-top: 40px;
  max-width: clamp(60vw, 1000px, 100vw);
}

.calc-section {
  flex: 1 1 0;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  padding: 1rem 0.5rem 1rem 0.5rem;
}

.calc-section > label:not(:first-of-type) {
  margin-top: 20px;
}

label {
  color: var(--clr-dark-grayish-cyan);
}

input {
  display: block;
  background-color: var(--clr-very-light-grayish-cyan);
  color: var(--clr-very-dark-cyan);
  width: 100%;
  border: none;
  border-radius: 5px;
  text-align: right;
  padding: 0.3rem 1rem 0.3rem 3rem;
  font-size: 1.5rem;
  background-repeat: no-repeat;
  background-size: 1rem;
  background-position: 1rem center;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input:not(.error):hover {
  outline: solid var(--clr-light-grayish-cyan);
}

input:not(.error):focus {
  outline-color: var(--clr-strong-cyan);
}

.error {
  background-color: rgba(255, 0, 0, 0.2);
  outline: solid red;
}

input::placeholder {
  color: var(--clr-grayish-cyan);
}

.errorMassage {
  color: red;
  align-self: end;
  font-size: 0.7rem;
}

.button-container {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.button-container button,
.button-container input {
  border: none;
  flex: 1 1 7rem;
  height: 3rem;
  font-size: 1.5rem;
  border-radius: 5px;
}

.button-container input {
  padding: 0.3rem 1rem 0.3rem 1rem;
}

.button-container input::placeholder {
  text-align: center;
}

.button-container button {
  background-color: var(--clr-very-dark-cyan);
  color: var(--clr-white);
  cursor: pointer;
}

.button-container button:focus,
.button-container button:active,
.button-container button[aria-selected='true'] {
  background-color: var(--clr-strong-cyan);
  color: var(--clr-very-dark-cyan);
}

.button-container button:hover {
  background-color: var(--clr-light-grayish-cyan);
  color: var(--clr-very-dark-cyan);
}

.result-section {
  flex: 1 1 0;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  background-color: var(--clr-very-dark-cyan);
  padding: 2rem;
  border-radius: 9px;
  min-width: 100px;
}

.total-category {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.total-category:not(:first-of-type) {
  margin-top: 1rem;
}

.total-category-text {
  color: var(--clr-white);
}

.total-category-text > span {
  color: var(--clr-grayish-cyan);
  font-size: 0.8rem;
}

.total-category > span {
  display: inline-block;
  color: var(--clr-strong-cyan);
  text-overflow: ellipsis;
  overflow: hidden;
  font-size: 2.5rem;
}

#resetButton {
  background-color: var(--clr-strong-cyan);
  color: var(--clr-very-dark-cyan);
  height: 3rem;
  margin-top: 20px;
  border: none;
  border-radius: 4.5px;
}

#resetButton:focus,
#resetButton:active {
  background-color: var(--clr-light-grayish-cyan);
}

#resetButton:hover {
  background-color: var(--clr-light-grayish-cyan);
}

#resetButton:disabled {
  background-color: hsl(172, 67%, 45%, 0.2);
  cursor: not-allowed;
}

@media (min-width: 720px) {
}

@media (min-width: 480px) {
  body {
    padding-top: 3rem;
  }

  .content {
    flex-direction: row;
    gap: 30px;
  }

  .calc-section {
    padding: 0.5rem;
  }

  .total-category:not(:first-of-type) {
    margin-top: 2.5rem;
  }
}
</style>
