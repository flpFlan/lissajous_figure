<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
const canvax_w = window.innerWidth * 0.8
const canvax_h = window.innerHeight
const freqx = ref(1)
const freqy = ref(1)
const phasex = ref(0)
const phasey = ref(0)
const format = (value: number) => `${value / Math.PI}π`
const parse = (value: string) => Number(value.replace('π', '').trim()) * Math.PI
const N = ref(100)

function paint() {
    const x = computed(() => {
        let arr = new Array()
        for (let i = 0; i < N.value; i++) {
            let v = Math.sin(freqx.value * i + phasex.value);
            arr.push(v)
        }
        return arr
    })
    const y = computed(() => {
        let arr = new Array()
        for (let i = 0; i < N.value; i++) {
            let v = Math.sin(freqy.value * i + phasey.value);
            arr.push(v)
        }
        return arr
    })
    const canvas = <HTMLCanvasElement>document.querySelector('#canvas');
    const ctx = <CanvasRenderingContext2D>canvas.getContext('2d');
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.beginPath();
    for (let i = 0; i < N.value; i++) {
        ctx.moveTo(x.value[i] * 200 + canvax_w / 2, y.value[i] * 200 + canvax_h / 2);
        ctx.lineTo(x.value[i + 1] * 200 + canvax_w / 2, y.value[i + 1] * 200 + canvax_h / 2);
    }
    ctx.stroke();
}
onMounted(() => {
    paint()
})

let freqxInterval: number | undefined = undefined
let freqyInterval: number | undefined = undefined
let phasexInterval: number | undefined = undefined
let phaseyInterval: number | undefined = undefined
const setfrexInterval = (v: boolean) => v ? freqxInterval = setInterval(() => { freqx.value = (freqx.value + 0.001) % 101; paint() }, 100) : clearInterval(freqxInterval)
const setfreyInterval = (v: boolean) => v ? freqyInterval = setInterval(() => { freqy.value = (freqy.value + 0.001) % 101; paint() }, 100) : clearInterval(freqyInterval)
const setphasexInterval = (v: boolean) => v ? phasexInterval = setInterval(() => { phasex.value = (phasex.value + Math.PI / 100) % (2 * Math.PI + Math.PI / 100); paint() }, 100) : clearInterval(phasexInterval)
const setphaseyInterval = (v: boolean) => v ? phaseyInterval = setInterval(() => { phasey.value = (phasey.value + Math.PI / 100) % (2 * Math.PI + Math.PI / 100); paint() }, 100) : clearInterval(phaseyInterval) 
</script>

<template>
    <n-layout has-sider>
        <n-layout-sider>
            <n-space vertical style="margin: 0;padding: 0;">
                <h6>x 频率</h6>
                <n-checkbox size="small" label="auto" @update:checked="setfrexInterval" />
                <n-slider v-model:value="freqx" :step="1" @update:value="paint" />
                <n-input-number v-model:value="freqx" size="small" @update:value="paint" />
                <h6>x 相位</h6>
                <n-checkbox size="small" label="auto" @update:checked="setphasexInterval" />
                <n-slider v-model:value="phasex" :step="Math.PI / 100" :max="2 * Math.PI" :format-tooltip="format"
                    @update:value="paint" />
                <n-input-number v-model:value="phasex" size="small" :format="format" :parse="parse" @update:value="paint" />
                <h6>y 频率</h6>
                <n-checkbox size="small" label="auto" @update:checked="setfreyInterval" />
                <n-slider v-model:value="freqy" :step="1" @update:value="paint" />
                <n-input-number v-model:value="freqy" size="small" @update:value="paint" />
                <h6>y 相位</h6>
                <n-checkbox size="small" label="auto" @update:checked="setphaseyInterval" />
                <n-slider v-model:value="phasey" :step="Math.PI / 100" :max="2 * Math.PI" :format-tooltip="format"
                    @update:value="paint" />
                <n-input-number v-model:value="phasey" size="small" :format="format" :parse="parse" @update:value="paint" />
                <h6>N</h6>
                <n-slider v-model:value="N" :step="1" :max="200" @update:value="paint" />
                <n-input-number v-model:value="N" size="small" @update:value="paint" />
            </n-space>
        </n-layout-sider>
        <n-layout>
            <n-layout-content>
                <canvas id="canvas" :width="canvax_w" :height="canvax_h" />
            </n-layout-content>
        </n-layout>
    </n-layout>
</template>

<style scoped>
#canvas {
    background-color: aquamarine;
}
</style>
