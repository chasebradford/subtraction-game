<script setup>
import Timer from './Timer.vue'

import { ref, reactive } from 'vue'
import _ from 'lodash'

const ops = reactive({
  a: 0,
  b: 0,
});

const score = reactive({
  right: 0,
  total: 0,
});

const guesses = reactive([0,1,2,3]);
const correct = ref(true);

const state = ref("WAITING");

function shuffle() {
  const d = 1 + Math.floor(Math.random() * 9);
  ops.b = 1 + Math.floor(Math.random() * 9);
  ops.a = d + ops.b;

  const shift = Math.max(-d, Math.floor(Math.random() * 4) - 3);
  
  for (let i = 0; i < 4; ++i) {
    guesses[i] = i + shift + d;
  }
}

function timeout() {
  state.value = "finished";
}

shuffle();

function guess(n) {
  if (correct.value) {
    score.total += 1;
  }
  if (ops.a - ops.b == n) {
    if (correct.value) {
      score.right += 1;
    }
    correct.value = true;
  } else {
    correct.value = false;
  }

  if (correct.value) {
    shuffle();
  }
}

</script>

<template>

<div class="card">
  <div v-if="state == 'WAITING'">
    <h1>Ready?</h1>
    <button class="btn btn-primary start" @click='state="RUNNING"' style='white-space: nowrap;'>GO!</button>
  </div>
  <div v-if='state == "RUNNING"'>
    <h1 class='card-header'>Score <div class='float-right '>{{score.right}} / {{score.total}}</div></h1>
      
    <div class='card-body'>
      <h1>
        <span class='op-key'></span>
        <span class='operand'>{{ops.a}}</span>
      </h1>
      <h1>
        <span class='op-key'>-</span>
        <span class='operand'>{{ops.b}}</span>
      </h1>

      <div class="d-inline-flex justify-content-center">
        <div v-for='g in guesses' v-key='g'>
          <button class="btn guess" :class="{'btn-primary': correct || g==ops.a-ops.b, 'btn-secondary': !correct && g!=ops.a-ops.b}" @click="guess(g)">{{g}}</button>
        </div>
      </div>
      <hr>
      <timer @end='state = "FINISHED"'></timer>
    </div>
  </div>
  <div v-if='state == "FINISHED"'>
    <div class='card-body'>
      <h1>Final Score</h1>
      <h1>{{score.right}} out of {{score.total}}</h1>
    </div>
  </div>
</div>

</template>

<style scoped>
.card {
  height: 500px;
  width: 600px;
}
.card-header {
  text-align: left;
}

.start {
  padding-left: 20px;
  padding-right: 20px;
  font-size: 3em;
}

.guess {
  font-size: 3em;
  width: 2em;
  margin-left: 2px;
}

.op-key {
  display: inline-block;
  width: 50px;
}
.operand {
  display: inline-block;
  width: 75px;
  text-align: right;
}

.timer {
  font-size: 3em;
}

</style>
