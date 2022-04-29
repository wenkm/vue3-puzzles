<template>
    <div id="box" :style="`--size: ${size}px; --scale: ${scale}`">
        <div class="action">
            <div class="set">
                <button @click="number--" :disabled="number <= 3">-</button>
                <span>{{ number }}阶</span>
                <button @click="number++" :disabled="number >= 8">+</button>
            </div>
            <button @click="shuffleHandle" :disabled="!isStart">打乱</button>
            <button @click="startHandle" :disabled="isStart">开始</button>
            <button @click="isStart = false" :disabled="!isStart">停止</button>
            <!-- <button @click="autoHandle" :disabled="!isStart">自动</button> -->
            
        </div>
        <timer :start="isStart"></timer>
        <transition-group name="puzzle" tag="div" class="container" :style="{width: `${size}px`, height: `${size}px`}">
            <div class="item" :class="{empty: item === 0}" v-for="(item, index) in puzzles" :key="item" @mousedown="clickHandle(index, item)" @touchstart.prevent="clickHandle(index, item)" :style="`background-position: ${-100 * (item - 1)}px ${-100 * ~~((item - 1) / number)}px`">
                <span>{{ item }}</span>
            </div>
        </transition-group>
    </div>
</template>
<script setup>
import timer from '@/components/timer.vue';
import {ref, watch, nextTick} from 'vue';
const puzzles = ref([]),
    number = ref(3),
    size = ref(0),
    scale = ref(1),
    isStart = ref(false);
watch(
    number,
    v => {
        size.value = 100 * v;
        puzzles.value = [];
        for (let i = 1; i < v * v; i++) {
            puzzles.value.push(i);
        }
        puzzles.value.push(0);
        scale.value = size.value > window.innerWidth ? window.innerWidth / size.value : 1;
        isStart.value = false;
    },
    {
        immediate: true,
    },
);
function shuffle(arr) {
    return arr.sort(() => Math.random() - 0.5);
}
function shuffleHandle() {
    puzzles.value = shuffle(puzzles.value.filter(item => item !== 0));
    puzzles.value.push(0);
}
function clickHandle(index, item) {
    if (!isStart.value) {
        return;
    }
    const leftIndex = index - 1,
        rightIndex = index + 1,
        topIndex = index - number.value,
        bottomIndex = index + number.value;
    if (leftIndex >= 0 && puzzles.value[leftIndex] === 0) {
        puzzles.value[leftIndex] = item;
        puzzles.value[index] = 0;
    } else if (rightIndex <= Math.pow(number.value, 2) - 1 && puzzles.value[rightIndex] === 0) {
        puzzles.value[rightIndex] = item;
        puzzles.value[index] = 0;
    } else if (topIndex >= 0 && puzzles.value[topIndex] === 0) {
        puzzles.value[topIndex] = item;
        puzzles.value[index] = 0;
    } else if (bottomIndex <= Math.pow(number.value, 2) - 1 && puzzles.value[bottomIndex] === 0) {
        puzzles.value[bottomIndex] = item;
        puzzles.value[index] = 0;
    }
    if (isSuccess()) {
        timer.isStart = false;
        alert('牛逼！');
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
function startHandle() {
    isStart.value = true;
    shuffleHandle();
}
function chunkArr(arr, size) {
    const newArr = [];
    for (let i = 0; i < arr.length; i += size) {
        newArr.push(arr.slice(i, i + size));
    }
    return newArr;
}

</script>
<style lang="less">
html, body{
    margin: 0;
    padding: 0;
}
.container {
    display: flex;
    flex-wrap: wrap;
    border: solid 1px #000;
    overflow: hidden;
    transform: scale(var(--scale));
    transform-origin: 0 0;
}
.action {
    display: flex;
    column-gap: 10px;
    margin-bottom: 10px;
    align-items: center;
    flex-wrap: wrap;
    
}
button {
    background: linear-gradient(30deg, #f64af6, #c2da62);
    border: solid 1px #7a1e7a;
    border-radius: 4px;
    color: #fff;
    height: 30px;
    padding: 0 15px;
    user-select: none;
    &:disabled {
        opacity: 0.5;
    }
}
.set {
    display: flex;
    align-items: center;
    span {
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
    &:hover {
        opacity: 0.5;
    }
    span {
        padding: 5px 10px;
        background: rgba(0, 0, 0, 0.3);
        position: absolute;
        top: 0;
        left: 0;
        color: #fff;
        border-radius: 0 0 10px 0;
        font-size: 12px;
    }
    &.empty {
        background: none;
        box-shadow: none;
        span {
            display: none;
        }
    }
}
.puzzle-move {
    transition: 0.8s cubic-bezier(0.19, 1, 0.22, 1);
}
</style>
