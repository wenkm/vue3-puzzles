<template>
    <div id="box" :style="`--size: ${size}px`">
        <div class="action">
            <div class="set">
                <button @click="number--" :disabled="number <= 3">-</button>
                <span>{{number}}阶</span>
                <button @click="number++" :disabled="number >= 8">+</button>
            </div>
            <button @click="shuffleHandle">打乱</button>
        </div>
        <transition-group name="puzzle" tag="div" class="container" :style="{width: `${number * 100}px`, height: `${number * 100}px`}">
            <div class="item" :class="{empty: item === ''}" v-for="item, index in puzzles" :key="item" @mousedown="clickHandle(index, item)" :style="`background-position: ${-100 * (item - 1)}px ${-100 * ~~((item - 1) / number)}px`">
                <span>{{ item }}</span>
            </div>
        </transition-group>
    </div>
</template>
<script setup>
import {ref, watch} from 'vue';
const puzzles = ref([]),
    number = ref(3),
    size = ref(0);

watch(number, v => {
    if ('' === v) {
        return;
    }
    size.value = 100 * v;
    puzzles.value = [];
    for (let i = 1; i < v * v; i++) {
        puzzles.value.push(i);
    }
    shuffle(puzzles.value);
    puzzles.value.push('');
}, {
    immediate: true
});

function shuffle(arr) {
    return arr.sort(() => Math.random() - 0.5);
}
function shuffleHandle() {
    puzzles.value = shuffle(puzzles.value.filter(item => item !== ''));
    puzzles.value.push('');
}
function clickHandle(index, item) {
    const leftIndex = index - 1,
        rightIndex = index + 1,
        topIndex = index - number.value,
        bottomIndex = index + number.value;
    if (leftIndex >= 0 && puzzles.value[leftIndex] === '') {
        puzzles.value[leftIndex] = item;
        puzzles.value[index] = '';
    } else if (rightIndex <= Math.pow(number.value, 2) - 1 && puzzles.value[rightIndex] === '') {
        puzzles.value[rightIndex] = item;
        puzzles.value[index] = '';
    } else if (topIndex >= 0 && puzzles.value[topIndex] === '') {
        puzzles.value[topIndex] = item;
        puzzles.value[index] = '';
    } else if (bottomIndex <= Math.pow(number.value, 2) - 1 && puzzles.value[bottomIndex] === '') {
        puzzles.value[bottomIndex] = item;
        puzzles.value[index] = '';
    }
    if (isSuccess()) {
        alert('恭喜你，成功了！');
    }
}
function isSuccess() {
    const arr = puzzles.value;
    for (let i = 0; i < arr.length - 1; i++) {
        if (arr[i] !== i + 1) {
            return false;
        }
    }
    return true;
}
</script>
<style lang="less">
.container {
    display: flex;
    flex-wrap: wrap;
    perspective: 1200px;
    border: solid 1px #000;
    overflow: hidden;
}
.action{
    display: flex;
    gap: 10px;
    margin-bottom: 10px;
}
button{
    background: linear-gradient(30deg, #f64af6, #c2da62);
    border: solid 1px #7a1e7a;
    border-radius: 4px;
    color: #fff;
    height: 30px;
    padding: 0 15px;
    user-select: none;
    &:disabled{
        opacity: 0.5;
    }
}
.set{
    display: flex;
    align-items: center;
    span{
        margin: 0 10px;
    }
}
.item {
    width: 100px;
    height: 100px;
    background-color: #f5f5f5;
    box-shadow: 0 0 0 1px #000;
    background-image: url('@/assets/hui.jpg');
    background-size: var(--size) var(--size);
    position: relative;
    user-select: none;
    &:hover{
        transition: 0.2s;
        opacity: 0.5;
    }
    span{
        padding: 5px 10px;
        background: rgba(0, 0, 0, 0.3);
        position: absolute;
        top: 0;
        left: 0;
        color: #fff;
        border-radius: 0 0 10px 0;
        font-size: 12px;
    }
    &.empty{
        background: none;
        box-shadow: none;
        span{
            display: none;
        }
    }
}
.puzzle-move {
    transition: 0.8s cubic-bezier(0.19, 1, 0.22, 1);
}
</style>
