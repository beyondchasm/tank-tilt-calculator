<!-- src/routes/+page.svelte -->
<script setup>
    // Mode 1 바인딩
    let bDeg  = 3.4948
    let bAz   = 7.1602
    let cDeg  = 20.4192
    let cAz   = 7.2121
    let h     = 6.563
  
    // Mode 2 바인딩
    let bDeg2 = 3.4948
    let bAz2  = 7.1602
    let cDeg2 = 20.4192
    let cAz2  = 7.2121
    let l     = 21.089
  
    // 결과 문자열
    let result = ''
  
    const degToRad = (/** @type {number} */ deg) => deg * Math.PI / 180
  
    function calculateTiltFromHeight() {
      const B = degToRad(bDeg)
      const C = degToRad(cDeg)
      const D = degToRad(Math.abs(cAz - bAz))
      const L = h / (Math.tan(C) - Math.tan(B))
      const H1 = L * Math.tan(C)
      const E = Math.hypot(L, H1)
      const delta = E * Math.tan(D)
      const tiltPercent = (delta / H1) * 100
  
      result = `
  [모드 1] 계산 결과:
  수평거리 L: ${L.toFixed(4)} m
  상단 높이 H₁: ${H1.toFixed(4)} m
  관측 거리 E: ${E.toFixed(4)} m
  수평 이탈 δ: ${delta.toFixed(4)} m
  기울기 비율: ${tiltPercent.toFixed(4)} %
      `
    }
  
    function calculateTiltFromDistance() {
      const B = degToRad(bDeg2)
      const C = degToRad(cDeg2)
      const D = degToRad(Math.abs(cAz2 - bAz2))
      const H = l * (Math.tan(C) - Math.tan(B))
      const H1 = l * Math.tan(C)
      const E = Math.hypot(l, H1)
      const delta = E * Math.tan(D)
      const tiltPercent = (delta / H1) * 100
  
      result += `
  [모드 2] 계산 결과:
  탱크 높이 H: ${H.toFixed(4)} m
  상단 높이 H₁: ${H1.toFixed(4)} m
  관측 거리 E: ${E.toFixed(4)} m
  수평 이탈 δ: ${delta.toFixed(4)} m
  기울기 비율: ${tiltPercent.toFixed(4)} %
      `
    }
  </script>
  
  <style>
    :global(body) {
      font-family: Arial, sans-serif;
      max-width: 800px;
      margin: 20px auto;
      line-height: 1.6;
      padding: 10px;
    }
    .text-center { text-align: center; }
    fieldset {
      margin-bottom: 20px;
      border: 1px solid #ddd;
      padding: 10px;
      border-radius: 5px;
    }
    legend { font-weight: bold; }
    label { display: block; margin-top: 10px; }
    input {
      padding: 4px;
      margin-top: 4px;
      width: 100%;
      box-sizing: border-box;
    }
    button {
      margin-top: 15px;
      padding: 10px 15px;
      background-color: #3498db;
      color: white;
      border: none;
      cursor: pointer;
      width: 100%;
    }
    button:hover { background-color: #2980b9; }
    .result {
      margin-top: 20px;
      padding: 15px;
      background: #f1f1f1;
      border-left: 4px solid #3498db;
      white-space: pre-wrap;
      word-wrap: break-word;
    }
    @media (min-width: 600px) {
      input, button {
        width: auto;
        max-width: 300px;
      }
    }
    @media (min-width: 768px) {
      :global(body) { padding: 20px; }
      button { max-width: 200px; }
    }
  </style>
  
  <h1 class="text-center">탱크 기울기 계산기</h1>
  <p class="text-center">탱크의 관측 데이터를 입력하면 기울기를 계산합니다.</p>
  
  <fieldset>
    <legend>모드 1: 탱크 높이 기준 계산</legend>
    <label>하단 수직각 (deg):
      <input type="number" bind:value={bDeg} step="0.0001" />
    </label>
    <label>하단 수평각 (deg):
      <input type="number" bind:value={bAz} step="0.0001" />
    </label>
    <label>상단 수직각 (deg):
      <input type="number" bind:value={cDeg} step="0.0001" />
    </label>
    <label>상단 수평각 (deg):
      <input type="number" bind:value={cAz} step="0.0001" />
    </label>
    <label>탱크 높이 H (m):
      <input type="number" bind:value={h} step="0.0001" />
    </label>
    <button on:click={calculateTiltFromHeight}>계산하기 (높이 기반)</button>
  </fieldset>
  
  <fieldset>
    <legend>모드 2: 수평 거리 기준 계산</legend>
    <label>하단 수직각 (deg):
      <input type="number" bind:value={bDeg2} step="0.0001" />
    </label>
    <label>하단 수평각 (deg):
      <input type="number" bind:value={bAz2} step="0.0001" />
    </label>
    <label>상단 수직각 (deg):
      <input type="number" bind:value={cDeg2} step="0.0001" />
    </label>
    <label>상단 수평각 (deg):
      <input type="number" bind:value={cAz2} step="0.0001" />
    </label>
    <label>수평 거리 L (m):
      <input type="number" bind:value={l} step="0.0001" />
    </label>
    <button on:click={calculateTiltFromDistance}>계산하기 (수평 거리 기반)</button>
  </fieldset>
  
  {#if result}
    <div class="result">{result}</div>
  {/if}