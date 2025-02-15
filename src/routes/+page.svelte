<script>
    import { fly } from 'svelte/transition';
    import { afterUpdate, onMount } from 'svelte';
  
    let showOutput = false;
    let outputText = 'Something went wrong, please try again.';
    let darkTheme = true;
  
    onMount(() => {
      const savedTheme = localStorage.getItem('theme');
      if (savedTheme) {
        darkTheme = savedTheme === 'dark';
      }
  
      const savedCheckboxes = JSON.parse(localStorage.getItem('checkboxes'));
      if (savedCheckboxes) {
        document.getElementById('spoof-spaces').checked = savedCheckboxes.spoofSpaces;
        document.getElementById('spoof-punctuation').checked = savedCheckboxes.spoofPunctuation;
        document.getElementById('insert-zero-width-spaces').checked = savedCheckboxes.insertZeroWidthSpaces;
        document.getElementById('preview-spoof').checked = savedCheckboxes.previewSpoof;
      }
    });
  
    function convert() {
      outputText = convertText();
    }
  
    function toggleTheme() {
      darkTheme = !darkTheme;
      localStorage.setItem('theme', darkTheme ? 'dark' : 'light');
    }
  
    function saveCheckboxes() {
      const checkboxes = {
        spoofSpaces: document.getElementById('spoof-spaces').checked,
        spoofPunctuation: document.getElementById('spoof-punctuation').checked,
        insertZeroWidthSpaces: document.getElementById('insert-zero-width-spaces').checked,
        previewSpoof: document.getElementById('preview-spoof').checked
      };
      localStorage.setItem('checkboxes', JSON.stringify(checkboxes));
    }
  
    afterUpdate(() => {
      if (darkTheme) {
        document.body.classList.add('dark');
        document.body.classList.remove('light');
      } else {
        document.body.classList.add('light');
        document.body.classList.remove('dark');
      }
    });
  </script>
  
  <style>
    :global(body) {
      margin: 0;
      padding: 0;
      font-family: "Segoe UI", system-ui, sans-serif;
      background-color: var(--bg);
    }
  
    * {
      box-sizing: border-box;
    }
  
    main {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      max-width: 600px;
      margin: 0;
      padding: 30px;
      border-radius: 12px;
      text-align: center;
      background-color: var(--bg);
      color: var(--text);
      box-shadow: 0 0 5px 5px var(--shadow);
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
    }
  
    .header-container {
      display: flex;
      justify-content: space-between;
      align-items: center;
      width: 100%;
    }
  
    .theme-toggle-container {
      display: flex;
      align-items: center;
      gap: 8px;
    }
  
    .theme-toggle-container button {
      background: transparent;
      border: none;
      cursor: pointer;
      font-size: 1.2rem;
      color: var(--text);
      display: flex;
      align-items: center;
    }
  
    .theme-toggle-container .icon {
      font-size: 1.5rem;
      color: var(--text);
    }
  
    h1 {
      font-size: 2rem;
      margin-bottom: 20px;
      transition: transform 0.3s ease;
    }
  
    h1:hover {
      transform: scale(1.03);
    }
  
    textarea {
      background-color: var(--bg);
      color: var(--text);
      border: 1px solid var(--border);
      padding: 12px;
      width: 100%;
      font-size: 1rem;
      border-radius: 8px;
      resize: none;
      transition: border-color 0.3s ease, box-shadow 0.3s ease;
      margin-bottom: 20px;
    }
  
    textarea:focus {
      outline: none;
      border-color: var(--border);
      box-shadow: 0 0 6px var(--border);
    }
  
    button {
      background-color: var(--bg);
      color: var(--text);
      padding: 12px 24px;
      font-size: 1rem;
      font-weight: 600;
      border: 1px solid var(--border);
      border-radius: 8px;
      cursor: pointer;
      transition: transform 0.1s linear;
    }
  
    button:hover {
      transform: scale(1.03);
      box-shadow: 0 0 3px var(--border);
    }
  
    .checkbox-container {
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: center;
      margin-bottom: 20px;
    }
  
    .checkbox-container label {
      font-size: 0.95rem;
      display: flex;
      align-items: center;
      cursor: default;
      margin-bottom: 8px;
      margin-right: 5px;
    }
  
    .checkbox-container input[type="checkbox"] {
      accent-color: currentColor;
      margin-right: 8px;
    }
  
    .footer {
        display: flex;
        justify-content: space-between;
        align-items: center;
        position: relative;
        width: 100%;
        font-size: 0.9rem;
        color: gray;
        margin-top: 20px;
    }
  
    .footer a {
      text-decoration: none;
      color: inherit;
      cursor: pointer;
    }
  
    .footer a:hover {
      color: var(--text);
    }
  
    .github-icons {
      display: flex;
      gap: 10px;
    }
  
    .github-icons i {
      color: gray;
      font-size: 1.5rem;
      cursor: pointer;
    }
  
    .github-icons i:hover {
      color: var(--text);
    }
    
    .icon i {
        margin-right: 10px;
    }
  
    @media (max-width: 768px) {
      main {
        margin: 20px;
        padding: 20px;
      }
      h1 {
        font-size: 1.75rem;
      }
    }
  </style>
  
  <main class={darkTheme ? 'dark' : 'light'}>
    <div class="header-container">
      <h1>Unicode Spoofer</h1>
      <div class="theme-toggle-container">
        <button on:click={toggleTheme}>
          {#if darkTheme}
            <span class="icon"><i class="fa-solid fa-moon"></i> </span> Dark Mode
          {:else}
            <span class="icon"><i class="fa-regular fa-sun"></i> </span> Light Mode
          {/if}
        </button>
      </div>
    </div>
  
    <textarea
      id="input"
      rows="5"
      placeholder="Enter text here..."
      on:input={() => {
        showOutput = false;
      }}
    ></textarea>
  
    <div class="checkbox-container">
      <label>
        <input type="checkbox" id="spoof-spaces" on:change={saveCheckboxes} />
        Spoof Spaces
      </label>
      <label>
        <input type="checkbox" id="spoof-punctuation" on:change={saveCheckboxes} />
        Spoof Punctuation
      </label>
      <label>
        <input type="checkbox" id="insert-zero-width-spaces" on:change={saveCheckboxes} />
        Insert Zero‑Width Spaces
      </label>
    </div>

  
    <button on:click={() => { 
      showOutput = true; 
      convert(); 
    }}>
      Convert
    </button>
    <div class="checkbox-container">
        <label style="margin-top: 7px">
            <input class="preview" type="checkbox" id="preview-spoof" on:change={saveCheckboxes} />
            Preview Spoofed
        </label>
    </div>
  
    {#if showOutput}
      <textarea
        id="output"
        rows="5"
        readonly
        bind:value={outputText}
        transition:fly={{ y: 20, duration: 400 }}
      ></textarea>
    {/if}
    <div class="footer">
        <a href="https://www.youtube.com/@DarkMusic110" target="_blank">
          Made by @DarkMusic110
        </a>
        <div class="github-icons">
          <a href="https://github.com" target="_blank">
            <i class="fab fa-github"></i>
          </a>
        </div>
      </div>
  </main>
  