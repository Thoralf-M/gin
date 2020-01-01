<script>
  import { quintOut } from "svelte/easing";
  import { crossfade } from "svelte/transition";

  const [send, receive] = crossfade({
    duration: d => Math.sqrt(d * 200)
  });

  let uid = 1;
  let ginselected = false;
  let selecteditems = 0;

  let zutatens = [
    { id: uid++, auswahl: false, description: "London Gin", gin: true },
    { id: uid++, auswahl: false, description: "Destillierter Gin", gin: true },
    { id: uid++, auswahl: false, description: "Dry Gin", gin: true },
    { id: uid++, auswahl: false, description: "Zitrone" },
    { id: uid++, auswahl: false, description: "Tonic Water" },
    { id: uid++, auswahl: false, description: "Orange" },
    { id: uid++, auswahl: false, description: "Ingwer" },
    { id: uid++, auswahl: false, description: "Wodka" },
    { id: uid++, auswahl: false, description: "Eiswürfel" },
    { id: uid++, auswahl: false, description: "Gurke" }
  ];

  function remove(zutaten) {
    zutatens = zutatens.filter(t => t !== zutaten);
  }

  function mark(zutat, auswahl) {
    //detect amount of items
    if (auswahl) {
      selecteditems++;
    } else {
      selecteditems--;
    }

    //check if a gin sort is selected
    if (zutat.gin == true) {
      ginselected = !ginselected;
    }
    zutat.auswahl = auswahl;
    remove(zutat);
    zutatens = zutatens.concat(zutat);
  }

  function submit() {
    let auswahl = zutatens.filter(t => t.auswahl);
    console.log("Bestellung:", auswahl);
  }
</script>

<style>
  .board {
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-gap: 1em;
    max-width: 36em;
    margin: 0 auto;
		margin-top: 1em;
  }

  h2 {
    font-size: 2em;
    font-weight: 200;
    user-select: none;
    margin: 0 0 0.5em 0;
  }

  label {
    position: relative;
    line-height: 1.2;
    padding: 0.5em 2.5em 0.5em 2em;
    margin: 0 0 0.5em 0;
    border-radius: 2px;
    user-select: none;
    border: 1px solid hsl(240, 8%, 70%);
    background-color: hsl(240, 8%, 93%);
    color: #333;
  }

  input[type="checkbox"] {
    position: absolute;
    left: 0.5em;
    top: 0.6em;
    margin: 0;
  }

  .auswahl {
    border: 1px solid hsl(240, 8%, 90%);
    background-color: hsl(240, 8%, 98%);
  }

  .submit {
    margin-top: 1em;
  }
</style>

<div class="board">

  <div class="left">
    <h2>Gin Sorten</h2>
    {#each zutatens.filter(t => !t.auswahl && t.gin) as zutaten (zutaten.id)}
      <label
        in:receive={{ key: zutaten.id }}
        out:send={{ key: zutaten.id }}
        style="color: {ginselected ? 'grey' : ''}">
        <input
          type="checkbox"
          on:change={() => mark(zutaten, true)}
          disabled={ginselected} />
        {zutaten.description}
      </label>
    {/each}
    <h2>Zutaten</h2>
    {#each zutatens.filter(t => !t.auswahl && !t.gin) as zutaten (zutaten.id)}
      <label in:receive={{ key: zutaten.id }} out:send={{ key: zutaten.id }}>
        <input type="checkbox" on:change={() => mark(zutaten, true)} />
        {zutaten.description}
      </label>
    {/each}
  </div>

  <div class="right">
    <h2>Auswahl</h2>
    {#each zutatens.filter(t => t.auswahl) as zutaten (zutaten.id)}
      <label
        class="auswahl"
        in:receive={{ key: zutaten.id }}
        out:send={{ key: zutaten.id }}>
        <input type="checkbox" checked on:change={() => mark(zutaten, false)} />
        {zutaten.description}
      </label>
    {/each}
{#if selecteditems > 0 && ginselected == true}
    <button class="submit" on:click={submit}>Bestellen</button>
		{/if}
  </div>
</div>
