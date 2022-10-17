<script>
  import { onMount, prevent_default } from "svelte/internal";
  import Modal from "./Modal.svelte";
  // @ts-ignore
  import * as dictionary from "word-list-json";

  // @ts-ignore
  String.prototype.replaceAt = function (_index, _value) {
    return this.substring(0, _index) + _value + this.substring(_index + 1);
  };

  let wordLength = 5;
  let maxtries = wordLength + 1;
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
  let keyboardHtml;
  let buton;
  let dateStart;
  let dateEnd;
  let selectHtmlValue;
  let words = [];
  let wordCheckingOn = true;

  let popups = [];

  let themes = {
    light: {
      background_primary: "white",
      background_secondary: "#eee",
      text_primary: "#101010",
      text_secondary: "#fff",
      keyboard_bg: "#eee",
    },
    dark: {
      background_primary: "#101010",
      background_secondary: "#111111",
      text_primary: "#fff",
      text_secondary: "#101010",
      keyboard_bg: "#aaa",
    },
    highContrast: {
      background_primary: "#101010",
      background_secondary: "#111111",
      text_primary: "#fff",
      text_secondary: "#101010",
      keyboard_bg: "#aaa",
      error: "#666",
    },
  };

  window.addEventListener("keydown", (e) => {
    prevent_default(e);
    let pressedKey = e.key.toLowerCase();
    if (pressedKey === "@" || pressedKey === "!") return;
    if (
      keysArr.some((arr) => arr.includes(pressedKey)) ||
      pressedKey === "enter" ||
      pressedKey === "backspace"
    )
      keyboardClick(pressedKey);
  });

  getWord();

  function wordLengthSelect() {
    getWord();
  }

  function getWord() {
    dateStart = Date.now();
    dateEnd = null;
    maxtries = 6;
    attempt = 1;
    word = "";
    cursorPos = [0, 0];
    state = "playing";
    modalMessage = "";
    modalTitle = "";

    tries = new Array(6);
    let blanks = " ".repeat(wordLength);
    tries.fill(blanks);

    console.log(dictionary);
    let wordsStartIndex = dictionary["lengths"][wordLength - 1];
    let wordsEndIndex = dictionary["lengths"][wordLength];
    words = dictionary.slice(wordsStartIndex, wordsEndIndex);
    word = words[Math.floor(Math.random() * words.length)];
    if (keyboardHtml)
      for (let keyHtml of keyboardHtml.querySelectorAll(".key")) {
        if (
          !keyHtml.classList.contains("enter") &&
          !keyHtml.classList.contains("backspace")
        )
          keyHtml.className = "key";
      }
  }

  function keyboardClick(keyPressed) {
    buton.blur();
    if (state !== "playing") return;
    if (keyPressed === "enter") {
      enterPress();
      return;
    }
    if (keyPressed === "backspace") {
      if (tries[cursorPos[0]][cursorPos[1]] === " ")
        if (cursorPos[1] > 0) cursorPos[1] -= 1;
      tries[cursorPos[0]] = tries[cursorPos[0]].replaceAt(cursorPos[1], " ");
      return;
    }
    tries[cursorPos[0]] = tries[cursorPos[0]].replaceAt(
      cursorPos[1],
      keyPressed
    );
    if (cursorPos[1] < wordLength - 1) cursorPos[1] += 1;
  }
  function getHighlight(row, index, letter) {
    if (row >= attempt - 1) return "";
    if (attempt === 1) return "";

    console.log(letter);
    let currWord = tries[row];

    let find = `${letter}`;
    let regexp = new RegExp(find, "g");
    let letterCount = (word.match(regexp) || []).length;

    let letterCountInTyped = 0;
    for (let i = 0; i < currWord.length; i++) {
      let l = currWord[i];
      if (l === letter && word[i] === letter) {
        letterCountInTyped++;
        if (index === i) return "highlight-success highlight white";
      }
    }

    for (let i = 0; i < currWord.length; i++) {
      let l = currWord[i];
      if (l === letter && word[i] != letter && word.includes(l)) {
        letterCountInTyped++;
        if (letterCountInTyped <= letterCount && index === i)
          return "highlight-warning highlight white";
      }
    }
    return "highlight-error highlight white";
  }
  function hideModal() {
    state = "playing";
    getWord();
  }
  function enterPress() {
    if (!words.includes(tries[cursorPos[0]]) && wordCheckingOn) {
      addPopup("To nie słowo");
      console.log(popups);
      return;
    }
    if (!tries[cursorPos[0]].includes(" ")) {
      if (tries[cursorPos[0]] === word) {
        tries[cursorPos[0]] = tries[cursorPos[0]];
        state = "win";
      }
      let typedWord = tries[cursorPos[0]];
      cursorPos[0] += 1;
      cursorPos[1] = 0;
      attempt += 1;

      tries[cursorPos[0]] = tries[cursorPos[0]];

      console.log(keyboardHtml);

      for (let index = 0; index < typedWord.length; index++) {
        let letter = typedWord[index];
        let keyHtml = keyboardHtml.querySelector(`div[data-key="${letter}"]`);

        // console.log(index, letter, keyHtml);
        if (word.includes(letter)) {
          if (word[index] === letter) {
            keyHtml.className = "key highlight-success white";
          } else {
            if (!keyHtml.className.includes("success"))
              keyHtml.className = "key highlight-warning white";
          }
        } else {
          keyHtml.className = "key highlight-error white";
        }
      }

      if (state === "win") {
        console.log("WIN");
        modalTitle = "Wygrana!";
        dateEnd = Date.now();
        let time = (dateEnd - dateStart) / 1000;
        modalMessage = `Slowo: ${word} \n Ilość prób: ${
          attempt - 1
        } \n Czas: ${time.toFixed(2)}s`;
        return;
      }
      if (attempt > maxtries) {
        console.log("GAME OVER");
        state = "gameover";
        modalTitle = "Przegrana";
        modalMessage = `Slowo: ${word}`;
      }
    }
    return;
  }

  function addPopup(text) {
    popups.push(text);
    popups = popups;
    window.setTimeout(() => {
      popups.shift();
      popups = popups;
    }, 1900);
  }

  onMount(() => {
    document.getElementById("theme").addEventListener("change", (e) => {
      console.log(selectHtmlValue);
      var r = document.querySelector(":root");
      r.style.setProperty(
        "--background-primary",
        themes[selectHtmlValue].background_primary
      );
      r.style.setProperty(
        "--background-secondary",
        themes[selectHtmlValue].background_secondary
      );
      r.style.setProperty(
        "--text-primary",
        themes[selectHtmlValue].text_primary
      );
      r.style.setProperty(
        "--text-secondary",
        themes[selectHtmlValue].text_secondary
      );
      r.style.setProperty("--keyboard-bg", themes[selectHtmlValue].keyboard_bg);
    });
  });
</script>

<Modal
  isOn={state !== "playing"}
  title={modalTitle}
  message={modalMessage}
  {hideModal}
/>

<div class="container">
  <div
    class="popups-box"
    style="display: {popups.length > 0 ? 'flex' : 'none'};"
  >
    {#each popups as popup}
      <p class="popup">{popup}</p>
    {/each}
  </div>
  <div class="panel">
    <div>
      <p>
        Długość: <input
          class="input-number"
          type="number"
          bind:value={wordLength}
          on:change={wordLengthSelect}
          max="9"
          min="4"
        />
      </p>
    </div>
    <div>
      <p>{word}</p>
    </div>
    <div>
      <div
        class="random-button"
        on:click={getWord}
        bind:this={buton}
        tabindex="-1"
      >
        Losuj
      </div>
    </div>
    <div class="theme-select">
      <select bind:value={selectHtmlValue} id="theme">
        <option value="light">jasny motyw</option>
        <option value="dark">ciemny motyw</option>
        <option value="highContrast">wysoki kontrast</option>
      </select>
    </div>
    <div class="word-check-box">
      <label for="word-checking">sprawdzanie pisowni</label>
      <input
        type="checkbox"
        name="word-checking"
        bind:checked={wordCheckingOn}
      />
    </div>
  </div>

  <div class="game" style="width: {wordLength * 50 + 10}px">
    {#each tries as row, i}
      <div class="row">
        {#each row as letter, j}
          <div
            class="letter-box {getHighlight(i, j, letter)} 
          {letter !== ' ' ? 'highlight-letter' : ''}"
          >
            {letter}
          </div>
        {/each}
      </div>
    {/each}
  </div>

  <div class="keyboard" bind:this={keyboardHtml}>
    {#each keysArr as keysRow, i}
      <div class="keyboard-row row{i + 1}">
        {#each keysRow as key}
          {#if key === "!"}
            <div class="key enter" on:click={() => keyboardClick("enter")}>
              ENTER
            </div>
          {:else if key === "@"}
            <div
              class="key backspace"
              on:click={() => keyboardClick("backspace")}
            >
              &lt;-
            </div>
          {:else}
            <div class="key" data-key={key} on:click={() => keyboardClick(key)}>
              {key}
            </div>
          {/if}
        {/each}
      </div>
    {/each}
  </div>
</div>
