<template>
    <div>
      <h1>TIC-TAC-TOE</h1>
      <h2 v-if="winner">Le joueur {{winner}} à WIN :)</h2>
      <h2 v-else>Le joueur qui a {{player}} joue</h2>
      <button @click="reset">Recommencer</button>
      <div class="squares">
        <div  v-for="x in 3" :key="x">
          <button class="square" v-for="y in 3" :key="y" @click="move(x,y)"> {{ squares[x-1][y-1] }}</button>

        </div>
      </div>

      <div>
        <h3>Récapitulatif des victoires</h3>
          <div v-for="(x,i) in history" :key="i">
            <p>Game {{i+1}}: {{x}} a gagné</p>

          </div>
      </div>

    </div>
</template>

<script>

import {computed, ref, watch, onMounted} from 'vue';

const calculateWinner = squares => {
  const possibility= [
    [0,1,2],
    [3,4,5],
    [6,7,8],
    [0,3,6],
    [1,4,7],
    [2,5,8],
    [0,4,8],
    [2,4,6],

  ]


  for(var i=0; i<possibility.length; i++){
    const [a,b,c]=possibility[i];
    if(squares[a] && squares[a]===squares[b] && squares[a]===squares[c]){
        return squares[a];
    }
  }
  return null;
}


export default {
  name: "Board",
  setup() {

    const player= ref('X');


      const squares= ref([
          ["","",""],
          ["","",""],
          ["","",""]
      ]);

      const winner=computed(() => calculateWinner(squares.value.flat()))



      function move(x,y) {

        if(!winner.value){
          if (squares.value[x - 1][y - 1] !== 'X' && squares.value[x - 1][y - 1] !== 'O') {
            squares.value[x - 1][y - 1] = player.value;
            player.value=player.value==='O' ? 'X' : 'O';
          }
        }
      }

      function reset() {
        player.value='X';
        squares.value=[
          ["","",""],
          ["","",""],
          ["","",""]
        ];
      }

      const history=ref([]);
      watch(winner, (current, previous) => {
        if(current && !previous){
          history.value.push(current);
          localStorage.setItem('history', JSON.stringify(history.value));
        }
    })


    onMounted( () => {
      history.value=JSON.parse(localStorage.getItem('history')) ?? [];
    });


      return { squares, player, move, winner, reset, history }
  },


}
</script>

<style scoped>

.squares {
  display: grid;
  grid-template-columns: repeat(1, minmax(0,1fr));
  width: 50%;

  margin: 20px auto 0;
}

.squares div {
  margin: 0 auto;
}

.square{
  background: #fff;
  border: 1px solid #999;
  float: left;
  font-size: 70px;
  font-weight: bold;
  line-height: 34px;
  height: 100px;
  margin-right: -1px;
  margin-top: -1px;
  padding: 0;
  text-align: center;
  width: 100px;
}

</style>