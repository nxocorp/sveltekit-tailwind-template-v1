<script>

  import { onMount } from 'svelte';

  // type Note = {
  //   id: String,
  //   blob: String
  // };

  let key = '';
  let value = '';
  // let noteArray: Note[] = [];
  let noteArray = [];
  let localStorageKeys = [];

  // Load all keys from LocalStorage
  function loadLocalStorageKeys() {
    localStorageKeys = Object.keys(localStorage);
  }

  // Save data to LocalStorage in note_array format
  function saveData() {
    // Split the textarea input into lines, filtering out any empty lines
    const lines = value.split('\n').filter(line => line.trim() !== '');

    // Ensure lines are greater than 0 before mapping
    if (lines.length > 0) {
      const noteArray = lines.map((line, index) => {
        console.log("Processing line:", line, "at index:", index+1);
        return{
          id: String(index+1).padStart(4, '0'), // Ensure the ID is always 4 digits, starting from "0001"
          blob: line.trim() // Trim any whitespace around each line
        }
      });

      const jsonValue = {
        note_array: noteArray
      };

      localStorage.setItem(key, JSON.stringify(jsonValue));
      key = '';
      value = '';
      alert('Data saved successfully!');

      loadLocalStorageKeys(); // Update the list of keys
    } else {
      alert('Please enter at least one line of text.');
    }
  }

  // Retrieve data from LocalStorage based on the selected key
  function retrieveData(selectedKey) {
    const storedValue = localStorage.getItem(selectedKey);
    if (storedValue) {
      try {
        const parsedValue = JSON.parse(storedValue);

        if (parsedValue.note_array && Array.isArray(parsedValue.note_array)) {
          noteArray = parsedValue.note_array;
        } else {
          noteArray = [];
          alert('Stored JSON structure is invalid.');
        }
      } catch (error) {
        noteArray = [];
        alert('Error parsing stored JSON.');
      }
    } else {
      noteArray = [];
      alert('No value found for the selected key.');
    }
  }
  
  // Initialize the list of keys on component mount
  onMount(() => {
    loadLocalStorageKeys();
  });

</script>

<div>
  <form on:submit|preventDefault={saveData}>
    <input type="text" bind:value={key} placeholder="Key" required />

    <textarea bind:value={value} placeholder="Enter list of items, one per line" required></textarea>

    <button type="submit">Save</button>
  </form>

  <h2>Available Keys</h2>
  <ul>
    {#each localStorageKeys as storageKey}
      <li>
        <a href="##" on:click|preventDefault={() => retrieveData(storageKey)}>{storageKey}</a>
      </li>
    {/each}
  </ul>

  <h2>Retrieved Data</h2>
  <ul>
    {#each noteArray as note}
      <li>({note.id}):{note.blob}</li>
    {/each}
  </ul>
</div>