<script>
  import * as d3 from "d3";
  export let model_break
  export let show_true

  const width = 528;
  const height = 300;
  const marginTop = 20;
  const marginRight = 30;
  const marginBottom = 20;
  const marginLeft = 40;

  let k = 100;
  
  let svg;
  let gx;
  let gy;
  
  $: accuracy_score = accuracy(model_break);

  $: x = d3
    .scaleLinear()
    .domain([0, d3.max(model_break, (d) => d.petal_width)])
    .range([marginLeft, width - marginRight])

  $: y = d3
    .scaleLinear()
    .domain([0, d3.max(model_break, (d) => d.petal_length)])
    .range([height - marginBottom, marginTop])

  $: d3.select(gx).call(d3.axisBottom(x))
  $: d3.select(gy).call(d3.axisLeft(y))

  function accuracy(data) {
    let correct = 0;
    if (data.length !== 0) {
      for (let i = 2750; i >= 2601; i = i - 1) {
        if (data[i].class === data[i].predicted) {
          correct = correct + 1;
        }
      }
      return Math.round((correct / 150).toFixed(2) * 100);
    }
  }

  function button_text(status) {
    if(status === false) {
      return "Show Actual Data"
    } else if (status === true) {
      return "Hide Actual Data"
    }
  }
</script>

<div class = "plot">
  <svg
      bind:this = {svg}
      {width}
      {height}
      viewBox = "0 0 {width} {height}"
      style = "max-width: 100%; height: auto;"
      overflow = "visible"
    >

  <g bind:this = {gx} transform = "translate(0, {height - marginBottom})"/>
  <g bind:this = {gy} transform = "translate({marginLeft}, 0)"/>

  {#if model_break.length !== 0}
    {#each model_break as d, i}
      {#if d.type === "boundary"}
        {#if d.predicted === "Iris-setosa"}
          <rect key = {i} width = 15 height = 10 x = {x(d.petal_width)} y = {y(d.petal_length) - 10} fill = "#4059AD" r = "4.5"/>
        {/if}
        {#if d.predicted === "Iris-versicolor"}
          <rect key = {i} width = 15 height = 10 x = {x(d.petal_width)} y = {y(d.petal_length) - 10} fill = "#97D8C4" r = "4.5"/>
        {/if}
        {#if d.predicted === "Iris-virginica"}
          <rect key = {i} width = 15 height = 10 x = {x(d.petal_width)} y = {y(d.petal_length) - 10} fill = "#F4B942" r = "4.5"/>
        {/if}
      {/if}
      {#if show_true == false && d.type === "point"}
        {#if d.predicted === "Iris-setosa"}
          <circle key = {i} cx = {x(d.petal_width)} cy = {y(d.petal_length)} fill = "#4059AD" stroke = "#000" r = "4.5"/>
        {/if}
        {#if d.predicted === "Iris-versicolor"}
          <circle key = {i} cx = {x(d.petal_width)} cy = {y(d.petal_length)} fill = "#97D8C4" stroke = "#000" r = "4.5"/>
        {/if}
        {#if d.predicted === "Iris-virginica"}
          <circle key = {i} cx = {x(d.petal_width)} cy = {y(d.petal_length)} fill = "#F4B942" stroke = "#000" r = "4.5"/>
        {/if}
      {/if}
      {#if show_true === true && d.type === "point"}
        {#if d.class === "Iris-setosa"}
          <circle key = {i} cx = {x(d.petal_width)} cy = {y(d.petal_length)} fill = "#4059AD" stroke = "#000" r = "4.5"/>
        {/if}
        {#if d.class === "Iris-versicolor"}
          <circle key = {i} cx = {x(d.petal_width)} cy = {y(d.petal_length)} fill = "#97D8C4" stroke = "#000" r = "4.5"/>
        {/if}
        {#if d.class === "Iris-virginica"}
          <circle key = {i} cx = {x(d.petal_width)} cy = {y(d.petal_length)} fill = "#F4B942" stroke = "#000" r = "4.5"/>
        {/if}
      {/if}
    {/each}
  {/if}

  <text x = 225 
    y = 330 
    font-size = 18
    style = "text-align:center">Petal Width</text>
    <text 
    font-size = 18
    x = 10
    y = -205
    style = "writing-mode: vertical-lr; transform: rotate(180deg);">Pedal Length</text>
    <!-- <g class = "legend" stroke = "#000">
      <text x = 1030 y = 58 font-size = 20>Legend</text>
      <circle cx = 1000 cy = 100 fill = "#4059AD" stroke = "#000" r = "13"/>
      <text x = 1030 y = 108 font-size = 20>Iris-setosa</text>
      <circle cx = 1000 cy = 150 fill = "#97D8C4" stroke = "#000" r = "13"/>
      <text x = 1030 y = 158 font-size = 20>Iris-versicolor</text>
      <circle cx = 1000 cy = 200 fill = "#F4B942" stroke = "#000" r = "13"/>
      <text x = 1030 y = 208 font-size = 20>Iris-virginica</text>
    </g> -->

  <g class = "accuracy_score">
    <text font-weight = bold>Accuracy Score: {accuracy_score}%</text>
  </g>

  </svg>
</div>

<!-- <div class = "overlay">
   <div style = "transform: translate(-16.5%, -40%)">
    <button bind:this = {button} on:click = {() => {show_true = !show_true}} style = "margin: 10px" 
      class = "
      bg-white 
      hover:bg-gray-100 
      text-gray-800 
      font-semibold py-2 px-4 border 
      border-gray-400 rounded shadow"
      >
      {button_t}
    </button>
  </div>
</div> -->

<style lang="postcss">
  rect {
    position: relative;
    z-index: 6;
  }
  circle {
    position: relative;
    z-index: 3;
    filter: brightness(75%);
  }
  button {
    margin-bottom: 1em;
    transform: translate(0, 150%)
  }
  button:hover {
    cursor: pointer;
  }
  #button99, #button100 {
    width: 40px;
  }
  .k-button {
    transform: translate(0, 40%);
    text-align: center;
  }
  .label {
    transform: translate(0, 200%);
  }
</style>