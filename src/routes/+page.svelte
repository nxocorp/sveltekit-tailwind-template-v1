<script>
  let key = '';
  let value = '';
  let noteArray = [];

  // Convert list to note_array format and save data to LocalStorage
  function saveData() {
    const lines = value.split('\n').filter(line => line.trim() !== '');
    const noteArray = lines.map((line, index) => ({
      id: String(index + 1).padStart(4, '0'),
      blob: line
    }));

    const jsonValue = {
      note_array: noteArray
    };

    localStorage.setItem(key, JSON.stringify(jsonValue));
    key = '';
    value = '';
    alert('Data saved successfully!');
  }

  // Retrieve data from LocalStorage and parse it as JSON
  function retrieveData() {
    const storedValue = localStorage.getItem(key);
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
      alert('No value found for the given key.');
    }
  }
</script>

<form on:submit|preventDefault={saveData}>
  <input type="text" bind:value={key} placeholder="Key" required />

  <textarea bind:value={value} placeholder="Enter list of items, one per line" required></textarea>

  <button type="submit">Save</button>
</form>

<button on:click={retrieveData}>Retrieve Data</button>

<ul>
  {#each noteArray as note}
    <li>{note.blob}</li>
  {/each}
</ul>
