<template>
    <div>{{timer}}</div>    
</template>
<script setup>
import {ref, watch} from 'vue';
const timer = ref(0);
let timeBegin = 0, reqTimer = null;
const props = defineProps(['start']);
watch(() => props.start, (v) => {
    if (v) {
        timeBegin = +new Date();
        startTimer();
    } else {
        cancelAnimationFrame(reqTimer);
    }
});
function startTimer() {
    timer.value = (+new Date() - timeBegin) / 1000;
    reqTimer = requestAnimationFrame(startTimer);
}
</script>