<script setup>
import {ref, toRaw} from "vue";
import {intersectionWith, isEmpty, isEqual} from "lodash";



const allSlot = 9;
const playerX = 'X';
const playerO = 'O';
const playerXPositions = ref([]);
const playerOPositions = ref([]);
const playersSignature = [playerX,playerO];
const actualMarker = ref(null)
const theMessage = ref(null);
const messageBlockVisibility = ref(false)

/**
 * @description Switch players and set the actual marker.
 * @returns {string}
 */
const playerSwitcher = (row) => {
  let player;
  if(actualMarker.value){
    // Change the actualMarker
    actualMarker.value = !actualMarker.value
    row.data.fieldValue = playerO;
    // Store the user occupied location
    playerOPositions.value.push({x:row.coords.x,y:row.coords.y, marker:row.data.fieldValue});
    player = playerX;
  }else{
    // Change the actualMarker
    actualMarker.value = !actualMarker.value
    row.data.fieldValue = playerX;
    // Store the user occupied location
    playerXPositions.value.push({x:row.coords.x,y:row.coords.y,marker:row.data.fieldValue});
    player = playerO;
  }
  return player;
}

/**
 * @description Match the user x selected coordinated with the winner combos
 */
const isPlayerXTheWinner = () => {
  let xIntersections = [];
  let filteredXCoords = toRaw(playerXPositions.value).map((v)=>{
    return {x:v['x'],y:v['y']}
  })
  let winningCombinations = toRaw(winningCombo.value);
  //console.log(filteredXCoords, winningCombinations);
  for( let winnerRow of winningCombinations){
    xIntersections.push(intersectionWith(filteredXCoords, winnerRow, isEqual));
  }

  //console.log('xIntersection',xIntersections);
  return xIntersections;
}

/**
 * @description Match the user o selected coordinated with the winner combos
 */
const isPlayerOTheWinner = () => {
  let oIntersections = [];
  let filteredOCoords = toRaw(playerOPositions.value).map((v)=>{
    return {x:v['x'],y:v['y']}
  })
  let winningCombinations = toRaw(winningCombo.value);
  //console.log(filteredOCoords, winningCombinations);
  for( let winnerRow of winningCombinations){
    oIntersections.push(intersectionWith(filteredOCoords, winnerRow, isEqual));
  }

  //console.log('oIntersection',oIntersections);
  return oIntersections;
}

/**
 * @description Announce the winner the draw!
 */
const winnerAnnouncement = () => {
    //console.log('playerOPositions:',toRaw(playerOPositions.value));
    //console.log('playerXPositions:',toRaw(playerXPositions.value));
    let threshold = 3;
    let maximumSlots = 9;
    let oIntersections = isPlayerOTheWinner();
    let xIntersections = isPlayerXTheWinner();
    let intersections = [{intersection:oIntersections,playerCode:'o'},{intersection:xIntersections,playerCode: 'x'}]
    for(let player of intersections){
      for(let coordinateGroup of player.intersection){
        if(coordinateGroup.length === threshold){
          // console.log(player.playerCode,coordinateGroup)
          theMessage.value = `The winner of the game is player: ${player.playerCode}`;
          messageBlockVisibility.value = true
          return true;
        }
      }
    }
    let slotCounter = 0;
    for(let row of board.value){
        for(let field of row.row){
          console.log(!isEmpty(field.data.fieldValue));
          if(!isEmpty(field.data.fieldValue)){
            slotCounter++;
            if(slotCounter === maximumSlots){
              theMessage.value = `The game is draw!`;
              messageBlockVisibility.value = true
              return true;
            }
          }
        }
    }
}

/**
 * @description Check the status of the game.
 */
const statusCheck = () => {
    let slots = allSlot;

    for (let i of board.value) {
      for (let j of i.row) {

        if (j.data.isFilled === false) {
          slots = slots-1;
        }
      }
    }
    winnerAnnouncement();
}

/**
 * @description Fill the board with the current marker.
 * Disable the fieldValue value update after change.
 * @param row
 */
const fillIn = (row) =>{
  if(!playersSignature.includes(row.data.fieldValue)){
    playerSwitcher(row);
    row.data.isFilled = !row.data.isFilled;
  }
  statusCheck();
}

const board = ref([
  {row:[
      {data:{fieldValue: '', isFilled: false},coords:{x:0,y:0}},
      {data:{fieldValue: '', isFilled: false},coords:{x:1,y:0}},
      {data:{fieldValue: '', isFilled: false},coords:{x:2,y:0}}
    ]
  },
  {row:[
      {data:{fieldValue: '', isFilled: false},coords:{x:0,y:1}},
      {data:{fieldValue: '', isFilled: false},coords:{x:1,y:1}},
      {data:{fieldValue: '', isFilled: false},coords:{x:2,y:1}}
    ]
  },
  {row:[
      {data:{fieldValue: '', isFilled: false},coords:{x:0,y:2}},
      {data:{fieldValue: '', isFilled: false},coords:{x:1,y:2}},
      {data:{fieldValue: '', isFilled: false},coords:{x:2,y:2}}
    ]
  },
]);

/**
 * @description Define the winning combinations
 */
const winningCombo = ref([
  [{x:0,y:0},{x:1,y:0},{x:2,y:0}], // Top row
  [{x:0,y:1},{x:1,y:1},{x:2,y:1}], // Middle row
  [{x:0,y:2},{x:1,y:2},{x:2,y:2}], // Bottom row
  [{x:0,y:0},{x:0,y:1},{x:0,y:2}], // Left column
  [{x:1,y:0},{x:1,y:1},{x:1,y:2}], // Middle column
  [{x:2,y:0},{x:2,y:1},{x:2,y:2}], // Right column
  [{x:0,y:0},{x:1,y:1},{x:2,y:2}], // Diagonal from top-left to bottom-right
  [{x:2,y:0},{x:1,y:1},{x:0,y:2}], // Diagonal from top-right to bottom-left
])

const startNewGame = ()=>{
  location.reload();
}

</script>
<template>
  <table class="table-auto center">
    <tbody>
    <tr v-show="messageBlockVisibility">
      <td class="header border-spacing-2 center" colspan="3">
        <h5>{{theMessage}}</h5>
        <p></p>
        <button class="rounded-full bg-yellow-500 content-center" @click="startNewGame()">Start a new game</button>
      </td>
    </tr>
    <tr>
      <td class="header border-spacing-2" colspan="3"><h6>Tic-Tac-Toe</h6></td>
    </tr>
    <tr v-for="i in board">
      <td v-for="j in i.row" class="button transform hover:bg-blue-900 border-spacing-2"
      @click="fillIn(j)"
      >{{j['data']['fieldValue']}}</td>
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
  border: solid 1px rgb(251 146 60);
}

.header{
  height: 5rem;
   button{
     padding: 0.25rem;
     margin:auto;
     display:block;

   }
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
    font-size: 1rem;
    color: #f6a50a;
    font-family: $font;
    letter-spacing: 0.2rem;
    text-transform: capitalize;
  }
  h5{
    margin-top: 0;
    text-align: center;
    font-size: 1rem;
    color: rgb(161 161 170);
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
.center {
  margin-left: auto;
  margin-right: auto;
}
</style>

