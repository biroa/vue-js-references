<script setup>
import {ref} from "vue";
const input = ref('')
const evaluation = ref('');
// const arithmetics = ['+','-','*','/'];
const buttons = ref(
    [
            {row:['+','-','*','/']},
            {row:['1','2','3','4']},
            {row:['5','6','7','8']},
            {row:['9','0','.','C']}
          ]);

/**
 * @description Clear the input when the user clicks on the 'C' clear
 */
const clearInput = ()=>{
  input.value = '';
}
/**
 * @description Calculate the expression based on the input values.
 */
const evaluateInput = () => {
  evaluation.value = eval(input.value);
  clearInput();
  input.value = evaluation.value;
}

/**
 * @description Define the expression that we need to calculate.
 */
const defineExpression = (i) => {
    if(i === 'C'){
      clearInput();
    }else{
      input.value += i;
    }
}


</script>
<template>
  <table class="table-fixed border-separate border-spacing-3 bg-slate-500">
    <tbody>
    <tr class="header">
      <td colspan="4" class="field">
        <input type="text" placeholder="let's calculate ..." v-model="input" disabled>
      </td>
    </tr>
    <tr class="header">
      <td class="button transform motion-safe:hover:scale-110 hover:bg-blue-900" @click="evaluateInput">=</td>
      <td colspan="3"><h1>Calculator</h1><h6>by Adam Biro</h6></td>
    </tr>
    <tr v-for="i in buttons">
      <td v-for="j in i.row" class="button transform motion-safe:hover:scale-110 hover:bg-blue-900" @click="defineExpression(j)">{{j}}</td>
    </tr>
    </tbody>
  </table>


</template>
<style lang="scss" scoped>

$font: ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";

.button{
  background-color: rgb(51 65 85);
  text-align: center;
  padding: 0.25rem;
  font-size: 3rem;
  width: 5rem;
  height: 5rem;
  color: aliceblue;
  font-family: $font;
  cursor: pointer;
}

.header{
  h1{
    text-align: center;
    padding: 0.55rem 0.55rem 0 0.55rem;
    font-size: 2.5rem;
    line-height:2.6rem;
    color: aliceblue;
    font-family: $font;
    letter-spacing: 0.2rem;
    text-transform: capitalize;
    margin-bottom: 0;
  }
  h6{
    margin-top: 0;
    text-align: center;
    font-size: 0.8rem;
    color: #f6a50a;
    font-family: $font;
    letter-spacing: 0.2rem;
    text-transform: capitalize;
  }
  background-color: rgb(21, 27, 36);
  .field{
    background-color: rgb(31 41 55);
    input{
      background: transparent;
      color: aliceblue;
      border:none;
      height: 3.5rem;
      font-size: 2em;
      width: 100%;
    }
  }
}
</style>
