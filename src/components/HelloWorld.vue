<template>
    <div class="hello">
        <div v-for="(items,x) in checkerboard" style="display:flex;justify-content:center;" :key="x">
            <div v-for="(item,y) in items" class="card" :class="`color_${item>128?128:item}`" :key="`${x}_${y}`">
                {{item?item:''}}
            </div>
        </div>
        <p style="margin-top:50px;">
            <button @click="operation('ArrowUp')">上</button>
        </p>
        <p>
            <button @click="operation('ArrowLeft')">左</button>
            <button @click="operation('ArrowDown')">下</button>
            <button @click="operation('ArrowRight')">右</button>
        </p>
    </div>
</template>

<script lang="ts">
import { defineComponent,onBeforeUnmount,onMounted,reactive } from "vue";
import _ from 'lodash';


export default defineComponent({
    name: "HelloWorld",
    props: {
        msg: String,
    },
    setup() {
        //接口、类型声明
        type Checkerboard = number[][];
        interface Slide {
            ArrowUp():void;
            ArrowRight():void;
            ArrowDown():void;
            ArrowLeft():void;
        }

        const checkerboard:Checkerboard = reactive([
            [0,4,16,0],
            [0,4,0,2],
            [0,2,4,4],
            [2,0,4,0]
        ])
 
        function arrSort(list:Array<number>){
            let arr = [];
            for (let i = list.length-1; i >= 0; i--) {
                if(i!==0 && list[i]===0){
                    arr.unshift(list.splice(i,1)[0])
                }
            }
            if(arr.length)list.unshift(...arr);
        }
        function arrCalculation(list:Array<number>):Array<number>{
            arrSort(list);
            for (let i = list.length-1; i >= 0; i--) {
                if(i!==0 && list[i]===list[i-1]){
                    list[i]*=2;
                    list[i-1]=0;
                    arrSort(list);
                }
            }
            return list;
        }
        const slide:Slide = {
            ArrowRight:function(){
                checkerboard.forEach((el,i)=>{
                    checkerboard[i] = arrCalculation(el);
                })
            },
            ArrowLeft:function(){
                checkerboard.forEach((el,i)=>{
                    checkerboard[i] = arrCalculation(el.reverse()).reverse();
                })
            },
            ArrowDown:function(){
                for (let y = 0; y < checkerboard[0].length; y++) {
                    let arrs:Array<number> = checkerboard.map(el=>el[y]);
                    arrs = arrCalculation(arrs)
                    checkerboard.forEach((el,i)=>{
                        checkerboard[i][y] = arrs[i];
                    })
                }
            },
            ArrowUp:function(){
                for (let y = 0; y < checkerboard[0].length; y++) {
                    let arrs:Array<number> = checkerboard.map(el=>el[y]).reverse();
                    arrs = arrCalculation(arrs).reverse()
                    checkerboard.forEach((el,i)=>{
                        checkerboard[i][y] = arrs[i];
                    })
                }
            },
        }
        function getZero():Checkerboard{
            let arr:Checkerboard = [];
            checkerboard.forEach((items,y)=>{
                items.forEach((item,x)=>{
                    if(item===0){
                        arr.push([y,x])
                    }
                })
            })
            return arr;
        }
        function operation(direction:String){
            const oldData:String = JSON.stringify(checkerboard)
            console.log(oldData);
            slide[direction as 'ArrowLeft'|'ArrowRight'|'ArrowUp'|'ArrowDown']()
            const newData:String = JSON.stringify(checkerboard)
            const arrZero = getZero();
            if(oldData!==newData){
                if(arrZero.length){
                    const [x,y] = arrZero[_.random(0,arrZero.length-1)];
                    checkerboard[x][y] = _.random(0,3)===0?4:2;
                    const [x2,y2] = arrZero[_.random(0,arrZero.length-1)];
                    checkerboard[x2][y2] = _.random(0,3)===0?4:2;
                }
            }
        }
        onMounted(()=>{
            document.onkeydown = function(event:any){
                if(slide[event.code as 'ArrowLeft'|'ArrowRight'|'ArrowUp'|'ArrowDown'])operation(event.code);
            }
        })
        onBeforeUnmount(()=>{
            document.onkeydown = null
        })
        return{
            checkerboard,
            operation,
        }
    },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .card{
        width:50px;
        height:50px;
        border: solid 1px;
        line-height: 50px;
        text-align: center;
    }
    .color_0{ background-color: rgba(255, 255, 255, 0.2); }
    .color_2{ background-color: rgba(255, 255, 255, 0.4); }
    .color_4{ background-color: rgba(243, 221, 184, 0.77); }
    .color_8{ background-color: rgba(255, 197, 100, 0.8); }
    .color_16{ background-color: rgba(245, 127, 43, 0.8); }
    .color_32{ background-color: rgb(245, 96, 39, 0.9); }
    .color_64{ background-color: rgb(247, 54, 39, 0.9); }
    .color_128{ background-color: rgba(247, 216, 77, 0.85); }
</style>
