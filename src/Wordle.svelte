<script>
  import { prevent_default } from 'svelte/internal';
  import Modal from './Modal.svelte';
  // @ts-ignore
  import words from './words.json';

  // @ts-ignore
  String.prototype.replaceAt = function(_index, _value){
    return this.substring(0, _index) + _value + this.substring(_index+1);
  }

  let wordLength = 5;
  let maxtries = 6;
  let attempt = 1;
  let word = "";
  let cursorPos = [0, 0];
  let state = "playings";

  const keysRow1 = "qwertyuiop";
  const keysRow2 = "asdfghjkl";
  const keysRow3 = "!zxcvbnm@";
  const keysArr = [keysRow1, keysRow2, keysRow3];

  let tries = new Array(6);
  let blanks = ' '.repeat(wordLength);
  tries.fill(blanks)

  window.addEventListener("keydown", (e)=>{
    prevent_default(e);
    let pressedKey = e.key.toLowerCase()
    if(pressedKey === "@" || pressedKey === "!") return;
    if(keysArr.some(arr => arr.includes(pressedKey)) || pressedKey === "enter" || pressedKey === "backspace")
      keyboardClick(pressedKey);
  });

  getWord()



  function wordLengthSelect(){
    getWord()
  }
  
  function getWord(){
    word = words[wordLength][Math.floor(Math.random()*words[wordLength].length)];
  }
  
  function keyboardClick(keyPressed){
    if(state === "gameover" || state === "win") return;
    if(keyPressed === "enter"){
      if(!tries[cursorPos[0]].includes(" ")){
        if(tries[cursorPos[0]] === word){
          tries[cursorPos[0]] = tries[cursorPos[0]]
          state = "win";
        }
        cursorPos[0] += 1;
        cursorPos[1] = 0;
        attempt += 1;

        tries[cursorPos[0]] = tries[cursorPos[0]]
        
        if(attempt > maxtries){
          console.log("GAME OVER");
          state = "gameover";
        }
      }
      return;
    }
    if(keyPressed === "backspace"){
      if(tries[cursorPos[0]][cursorPos[1]] === " ") 
        if(cursorPos[1] > 0)
          cursorPos[1] -= 1;
      tries[cursorPos[0]] = tries[cursorPos[0]].replaceAt(cursorPos[1], " ");
      return; 
    }
    tries[cursorPos[0]] = tries[cursorPos[0]].replaceAt(cursorPos[1], keyPressed)
    if(cursorPos[1] < wordLength - 1)
      cursorPos[1] += 1;

  }
  function getHighlight(row, index, letter){
    if(row < attempt - 1){
      if(word.includes(letter)){
        if(word[index] === letter) return "highlight-success highlight";   
        else return "highlight-warning highlight";
      }
      else return "highlight-error highlight";
    }
  }
  function hideModal(){
    state = "playing";
  }

</script>

<Modal isOn={state !== "playing"} title="You win" message={"siemano"} hideModal={hideModal}></Modal>

<div class="container">
  <div class="panel">
    <div>
      <p>Długość: <input class="input-number" type="number" bind:value={wordLength} on:change={wordLengthSelect} max="6" min="4"></p> 

    </div>
    <div>
      <p>{word}</p>
    </div>
    <div>
      <button class="random-button" on:click={getWord}>Losuj</button>
    </div>
  </div>
    
  <!-- 
    PLANSZA
  -->

  <div class="game" style="width: {wordLength*50 + 10}px">
    {#each tries as row, i}
      <div class="row">
        {#each row as letter, j}
          <div class="letter-box {getHighlight(i, j, letter)} {letter !== " " ? "highlight-letter" : ""}">{letter}</div>
        {/each}
      </div>
    {/each}
  </div>

  <!-- 
    KLAWIATURA
  -->
  <div class="keyboard">
    {#each keysArr as keysRow, i}
      <div class="keyboard-row row{i+1}">
        {#each keysRow as key}
          {#if key === "!"}
              <div class="key enter" on:click={()=>keyboardClick("enter")}>ENTER</div>
            {:else if key === "@"}
              <div class="key backspace" on:click={()=>keyboardClick("backspace")}>&lt;-</div>
            {:else} 
            <div class="key" on:click={()=>keyboardClick(key)}>{key}</div>
          {/if}
        {/each}
      </div>
    {/each}
  </div>
</div>

<style>
  .container{
    width: 100%;
    height: 100%;
    border: 1px solid black;
    padding: 10px;
    font-family: 'Clear Sans', 'Helvetica Neue', Arial, sans-serif;
  }
  .panel{
    margin: 0 auto;
    width: 50%;
    border: 1px solid black;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-evenly;
    box-shadow: 3px 3px 3px gray;
  }
  .panel > *{
    margin: 1px;
  }
  .input-number{
    width: 30px;
  }
  .random-button{
    background-color: rgb(200, 200, 200);
    padding: 5px;
    border-radius: 5px;
  }
  .game{
    margin: 0 auto;
    margin-top: 10px;
    min-height: 350px;
    /* border: 1px black solid; */
  }
  .row {
    height: 52px; 
    display: flex;
    flex-direction: row;

  }
  .letter-box{
    width: 50px;
    height: 50px;
    border: 1px solid black;
    margin: 1px;
    border-radius: 2px;
    background-color: #eee;
    font-size: 26px;
    text-align: center;
    line-height: 45px;
    color: #222;
    font-weight: 700;
    font-family: 'Clear Sans', 'Helvetica Neue', Arial, sans-serif;
    text-transform: uppercase;
    transform-style: preserve-3d;
    perspective: 1000px;
  }
  .keyboard{
    width: 500px;
    height: 180px;
    margin: 0 auto;
    margin-bottom: 20px;
    display: flex;
    flex-direction: column;
  }
  .keyboard-row{
    width: 500px;
    height: 58px;
    display: flex;
    margin: 0 auto;
    margin-bottom: 8px;
    flex-direction: row;
    justify-content: center;
  }
  .key {
    width: 43px;
    height: 58px;
    background-color: #eee;
    margin-right: 6px;
    line-height: 52px;
    text-align: center;
    border-radius: 5px;
  }

  .enter{
    font-size: 15px;
    width: 65px;
  }
  .backspace{
    font-size: 15px;
    width: 65px;
  }
  .highlight-error {
    background-color: #666;
  }
  .highlight-success {
    background-color: #6aaa64;
  }
  .highlight-warning{
    background-color: #f7da21;
  }
  .highlight-letter{
    border: 2px solid black;
    animation: type 0.4s ease-in-out;
  }
  .highlight {
    animation: flip 1s;
  }
  @keyframes type {
    0%   {transform: scale(1);}
    50%  {transform: scale(1.05);}
    100% {transform: scale(1);}
  }
  @keyframes flip {
    0%   {transform: rotateX(0deg);}
    50%  {transform: rotateX(90deg);}
    100% {transform: rotateX(0deg);}
  }
</style>