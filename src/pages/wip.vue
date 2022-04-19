<script setup lang="ts">
import {isDark} from "~/composables";

const { t } = useI18n()

import colors from "~/resources/htmlcolors.json";

const csscolors = ref(Object.entries(colors).map(function(color){return {name: color[0], hex: color[1]};}));
const color = ref({name: 'Start', hex: 'fff#'});
const inputcolor = ref('#BBBBBB');
const textcolor = ref('#BBBBBB');
const input = ref('');
const guessed = ref(false);
const result = ref(false);
const streak = ref(0);

const endsin = ref('');
const startswith = ref('');
const basicvalue = ref('');

const randomColor = () => {
  console.log(csscolors);
  color.value = csscolors.value[Math.floor(Math.random() * csscolors.value.length)];
  console.log('Color chosen:',color.value.name+' ('+color.value.hex+')');
  //$("body").css('background-color',color.value.hex);

  document.querySelector('body').setAttribute('style', 'background:'+color.value.hex)

  let newcolor = (color.value.hex.charAt(0) === '#') ? color.value.hex.substring(1, 7) : color.value.hex;
  let r = parseInt(newcolor.substring(0, 2), 16); // hexToR
  let g = parseInt(newcolor.substring(2, 4), 16); // hexToG
  let b = parseInt(newcolor.substring(4, 6), 16); // hexToB
  isDark.value = (((r * 0.299) + (g * 0.587) + (b * 0.114)) < 180);

  endsin.value = '';
  startswith.value = '';

  ['blue','white','green','red','orange','grey','gray','purple','brown'].forEach(function(c){
    if(color.value.name.includes(c) && color.value.name != c)endsin.value = c;
  });

  ['dark','light'].forEach(function(c){
    if(color.value.name.includes(c))startswith.value = c;
  });



}

const guess = () => {
  guessed.value = true;
  if(input.value === color.value.name){
    streak.value++;
    inputcolor.value = '#50bb54';
    result.value = true; //CORRECT
  }else{
    streak.value = 0;
    inputcolor.value = '#ea0000';
    input.value = color.value.name;
    result.value = false; //INCORRECT
  }

};

const tryEnter = (e) => {
  if (e.keyCode === 13) {
    if(guessed.value){
      //Reset
      guessed.value=false;
      randomColor();
      input.value = '';
      inputcolor.value ='#BBBBBB';
    }else{
      guess();
    }
  }
}

randomColor();
</script>

<template>
  <div :style="'background-color:'+color.hex">
    <div text-4xl>
      <div i-mdi-palette inline-block />
    </div>
    <p>
      <a rel="noreferrer" href="" target="">
        What's this color?
      </a>
    </p>
    <p>
      <em text-sm opacity-75>{{ t('intro.desc') }}</em>
    </p>

    <div py-4 />

    <div id="centercontainer" class="middle">
      <div id="innercontainer">
        <div class=" animate__animated animate__fadeIn animate__faster" id="answerbox" :style="'border-color:'+inputcolor">

          <input id="answerinput" v-model="input" @keyup="tryEnter" placeholder="Guess the color" autofocus>

        </div>

        <div py-2></div>
        <p v-if="endsin!==''" >Hint: it ends with {{ endsin }}</p>
        <p v-if="startswith!=='' && endsin == ''" >Hint: it starts with {{ startswith }}</p>


        <!--        <span v-if="result && !guessed" class="success-message" :style="'color:'+textcolor">Correct! Press Enter to continue.</span>-->
      </div>
    </div>

    <p>{{ color.hex }}</p>

    <div py-4 />

    <div v-if="streak>0" text-4xl class="animate__animated animate__fadeInUp ">
      Streak: {{ streak }}
    </div>

    <div py-4 />

  </div>

  <div class="sidelist">
    <div v-for="color in csscolors"><span>{{color.name}}</span></div>
  </div>
</template>

<route lang="yaml">
meta:
  layout: wip
</route>
