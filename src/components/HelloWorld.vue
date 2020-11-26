<template>
    <div class="hello">
        <div v-for="(items,x) in checkerboard" style="display:flex;justify-content:center;" :key="x">
            <div v-for="(item,y) in items" style="width:20px;height:20px;" :key="`${x}_${y}`">
                {{item}}
            </div>
        </div>
        <p>
            <button @click="operation('top')">上</button>
            <button @click="operation('bottom')">下</button>
            <button @click="operation('left')">左</button>
            <button @click="operation('right')">右</button>
        </p>
    </div>
</template>

<script lang="ts">
import { defineComponent,reactive } from "vue";
import _ from 'lodash';

export default defineComponent({
    name: "HelloWorld",
    props: {
        msg: String,
    },
    setup() {
        const checkerboard = reactive([
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
        function arrCalculation(list:Array<number>){
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
        function slide(direction:String){
            if(direction==='right'){
                checkerboard.forEach((el,i)=>{
                    checkerboard[i] = arrCalculation(el);
                })
            }
            else if(direction==='left'){
                checkerboard.forEach((el,i)=>{
                    checkerboard[i] = arrCalculation(el.reverse()).reverse();
                })
            }
            else if(direction==='bottom'){
                for (let y = 0; y < checkerboard[0].length; y++) {
                    let arrs:Array<number> = checkerboard.map(el=>el[y]);
                    arrs = arrCalculation(arrs)
                    checkerboard.forEach((el,i)=>{
                        checkerboard[i][y] = arrs[i];
                    })
                }
            }
            else if(direction==='top'){
                for (let y = 0; y < checkerboard[0].length; y++) {
                    let arrs:Array<number> = checkerboard.map(el=>el[y]).reverse();
                    arrs = arrCalculation(arrs).reverse()
                    checkerboard.forEach((el,i)=>{
                        checkerboard[i][y] = arrs[i];
                    })
                }
            }
        }
        function getZero(){
            let arr:Array<Array<number>> = [];
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
            slide(direction)
            const newData:String = JSON.stringify(checkerboard)
            if(oldData!==newData){
                const arrZero:Array<Array<number>> = getZero();
                if(arrZero.length){
                    const [x,y] = arrZero[_.random(0,arrZero.length-1)];
                    checkerboard[x][y] = _.random(0,2)===0?4:2;
                    const [x2,y2] = arrZero[_.random(0,arrZero.length-1)];
                    checkerboard[x2][y2] = _.random(0,2)===0?4:2;
                }
            }
        }
        console.log(checkerboard);
        return{
            checkerboard,
            operation
        }
    },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
