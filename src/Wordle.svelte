<script>
  // @ts-ignore
  import words from './words.json';

  let length = 5;
  let maxtries = 6;
  let word = "";
  const keysRow1 = "qwertyuiop";
  const keysRow2 = "asdfghjkl";
  const keysRow3 = "!zxcvbnm@";
  const keysArr = [keysRow1, keysRow2, keysRow3];


  function lengthSelect(){
    getWord()
  }
  
  function getWord(){
    word = words[length][Math.floor(Math.random()*words[length].length)];
  }
  getWord()

</script>


<div class="container">
  <div class="panel">
    <div>
      <p>Długość: <input class="input-number" type="number" bind:value={length} on:change={lengthSelect} max="6" min="4"></p> 

    </div>
    <div>
      <p>{word}</p>
    </div>
    <div>
      <button class="random-button" on:click={getWord}>Losuj</button>
    </div>
  </div>
  
  <div class="game" style="width: {length*50 + 10}px">
    {#each Array(maxtries) as row}
      <div class="row">
        {#each Array(length) as col}
          <div class="letter-box"></div>
        {/each}
      </div>
    {/each}
  </div>

  <div class="keyboard">
    {#each keysArr as keysRow, i}
      <div class="keyboard-row row{i+1}">
        {#each keysRow as key}
          {#if key === "!"}
              <div class="key enter">ENTER</div>
            {:else if key === "@"}
              <div class="key backspace">&lt;-</div>
            {:else} 
            <div class="key">{key}</div>
          {/if}
        {/each}
      </div>
    {/each}
  </div>
</div>

<style>
  .container{
    width: 100vw;
    height: auto;
    border: 1px solid black;
    padding: 10px;
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
  }
  .keyboard{
    width: 500px;
    height: 180px;
    margin: 0 auto;
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
</style>