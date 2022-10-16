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
  let state = "playing";
  let modalMessage = "siemano";
  let modalTitle = "E";

  const keysRow1 = "qwertyuiop";
  const keysRow2 = "asdfghjkl";
  const keysRow3 = "!zxcvbnm@";
  const keysArr = [keysRow1, keysRow2, keysRow3];

  let tries = new Array(6);
  let blanks = ' '.repeat(wordLength);
  tries.fill(blanks)
  let keyboardHtml;

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
    if(state !== "playing") return;
    if(keyPressed === "enter"){
      if(!tries[cursorPos[0]].includes(" ")){
        if(tries[cursorPos[0]] === word){
          tries[cursorPos[0]] = tries[cursorPos[0]]
          state = "win";
        }
        let before = tries[cursorPos[0]]
        cursorPos[0] += 1;
        cursorPos[1] = 0;
        attempt += 1;

        tries[cursorPos[0]] = tries[cursorPos[0]]

        console.log(keyboardHtml)
        for(let index = 0; index < before.length; index++){
          let letter = before[index];
          let keyHtml = keyboardHtml.querySelector(`div[data-key="${letter}"]`);
          console.log(index, letter, keyHtml)
          if(word.includes(letter)){
            if(word[index] === letter){
              keyHtml.className = "key highlight-success white";
            } 
            else{
              if(!keyHtml.className.includes("success"))
              keyHtml.className = "key highlight-warning white";
            } 
          }
          else {
            keyHtml.className = "key highlight-error white";
          }
        }
        
        if(attempt > maxtries){
          console.log("GAME OVER");
          state = "gameover";
          modalTitle = "Przegrana";
          modalMessage = `Slowo: ${word}`
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
    if(row < attempt - 1) {
      if(word.includes(letter)) {
        if(word[index] === letter) return "highlight-success highlight white";
        else return "highlight-warning highlight white";
      }
      else return "highlight-error highlight white";
    }
  }
  function hideModal(){
    state = "playing";
    getWord();
  }

</script>

<Modal isOn={state !== "playing"} title={modalTitle} message={modalMessage} hideModal={hideModal}>
</Modal>

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
  <div class="keyboard" bind:this={keyboardHtml}>
    {#each keysArr as keysRow, i}
      <div class="keyboard-row row{i+1}">
        {#each keysRow as key}
          {#if key === "!"}
              <div class="key enter" on:click={()=>keyboardClick("enter")}>ENTER</div>
            {:else if key === "@"}
              <div class="key backspace" on:click={()=>keyboardClick("backspace")}>&lt;-</div>
            {:else} 
            <div class="key" data-key={key} on:click={()=>keyboardClick(key)}>{key}</div>
          {/if}
        {/each}
      </div>
    {/each}
  </div>
</div>