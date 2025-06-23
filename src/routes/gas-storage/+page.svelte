<!-- src/routes/gas-storage/+page.svelte -->
<script lang="ts" setup>
  import * as Sidebar from '$lib/components/ui/sidebar';
  import AppSidebar from '$lib/components/app-sidebar.svelte';
  import * as Breadcrumb from '$lib/components/ui/breadcrumb';
  import { Separator } from '$lib/components/ui/separator';

  interface Comp { id: number; p: number; v1: number; n: number }
  interface Liq  { id: number; d: number; v2: number; n: number }
  interface Ctn  { id: number; v2: number; c: number; n: number }

  let compEntries: Comp[] = [];
  let liqEntries:  Liq[]  = [];
  let ctnEntries:  Ctn[]  = [];

  let A = 0, B = 0, C = 0;
  let W = 0, X = 0, Y = 0, Z = 0;
  let permMsg = '';

  let nextComp = 0, nextLiq = 0, nextCtn = 0;

  function addComp() {
    compEntries = [
      ...compEntries,
      // 기본값 p=1, v1=1, n=1
      { id: nextComp++, p: 1, v1: 1, n: 1 }
    ];
  }
  function addLiq() {
    liqEntries = [
      ...liqEntries,
      // 기본값 d=1, v2=1, n=1
      { id: nextLiq++, d: 1, v2: 1, n: 1 }
    ];
  }
  function addCtn() {
    ctnEntries = [
      ...ctnEntries,
      // 기본값 v2=1, c=1, n=1
      { id: nextCtn++, v2: 1, c: 1, n: 1 }
    ];
  }

  function remove<T extends {id:number}>(arr: T[], id: number, setter: (v:T[])=>void) {
    setter(arr.filter(e => e.id !== id));
  }

  function calcComp(e: Comp) {
    const q = (10 * e.p + 1) * e.v1 * e.n;
    A = compEntries.reduce((s, x) => s + (10*(x.p)+1)*(x.v1)*(x.n), 0);
    document.getElementById(`resComp${e.id}`)!.textContent = `개별: ${q.toFixed(3)} m³`;
    document.getElementById('resultQ')!.textContent = `A (총 압축): ${A.toFixed(3)} m³`;
  }

  function calcLiq(e: Liq) {
    const w = 0.9 * e.d * e.v2 * e.n;
    B = liqEntries.reduce((s, x) => s + 0.9*(x.d)*(x.v2)*(x.n), 0);
    document.getElementById(`resLiq${e.id}`)!.textContent = `개별: ${w.toFixed(3)} kg`;
    document.getElementById('resultB')!.textContent = `B (총 액화): ${B.toFixed(3)} kg`;
  }

  function calcCtn(e: Ctn) {
    const w = (e.v2 / e.c) * e.n;
    C = ctnEntries.reduce((s, x) => s + (x.v2/x.c)*(x.n), 0);
    document.getElementById(`resCtn${e.id}`)!.textContent = `개별: ${w.toFixed(3)} kg`;
    document.getElementById('resultC')!.textContent = `C (총 용기): ${C.toFixed(3)} kg`;
  }

  function calcToxic() {
    W = parseFloat((document.getElementById('w') as HTMLInputElement).value) || 0;
    X = parseFloat((document.getElementById('x') as HTMLInputElement).value) || 0;
    Y = parseFloat((document.getElementById('y') as HTMLInputElement).value) || 0;
    Z = parseFloat((document.getElementById('z') as HTMLInputElement).value) || 0;
    document.getElementById('resultToxic')!.innerHTML =
      `W=${W.toFixed(3)} kg<br>X=${X.toFixed(3)} kg<br>Y=${Y.toFixed(3)} m³<br>Z=${Z.toFixed(3)} m³`;
  }

  function checkPerm() {
    const val = A/500 + B/5000 + C/5000 + W/1000 + X/100 + Y/100 + Z/10;
    permMsg = val >= 1
      ? `⚠️ 허가대상 (값: ${val.toFixed(3)})`
      : `✅ 비허가대상 (값: ${val.toFixed(3)})`;
  }

  // 초기 항목 하나씩 기본값으로 추가
  addComp();
  addLiq();
  addCtn();
</script>

<Sidebar.Provider>
  <AppSidebar />

  <Sidebar.Inset>
    <header class="flex h-16 items-center gap-2 border-b px-4">
      <Sidebar.Trigger class="-ml-1" />
      <Separator orientation="vertical" class="mr-2 h-4" />
      <Breadcrumb.Root>
        <Breadcrumb.List>
          <Breadcrumb.Item class="hidden md:block">
            <Breadcrumb.Link href="/">Home</Breadcrumb.Link>
          </Breadcrumb.Item>
          <Breadcrumb.Separator class="hidden md:block" />
          <Breadcrumb.Item>
            <Breadcrumb.Page>가스 저장량 계산기</Breadcrumb.Page>
          </Breadcrumb.Item>
        </Breadcrumb.List>
      </Breadcrumb.Root>
    </header>

    <main class="p-4 md:p-6 max-w-4xl mx-auto space-y-8">
      <h1 class="text-2xl md:text-3xl font-bold text-center">
        가스 저장량 계산기 + 허가대상 판단
      </h1>

      <!-- 1. 압축가스 -->
      <fieldset class="border rounded-lg p-4 space-y-4">
        <legend class="font-medium">1. 압축가스 저장량 (A, m³)</legend>
        <div class="text-gray-600 text-sm">A = (10P+1)·V₁·N</div>
        <div class="space-y-2">
          {#each compEntries as e (e.id)}
            <div
              class="flex flex-col sm:flex-row sm:items-center sm:space-x-2 space-y-2 sm:space-y-0 border rounded p-2"
            >
              <input
                type="number"
                bind:value={e.p}
                placeholder="P (MPa)"
                class="w-full sm:w-24 border rounded px-2 py-1"
              />
              <input
                type="number"
                bind:value={e.v1}
                placeholder="V₁ (m³)"
                class="w-full sm:w-24 border rounded px-2 py-1"
              />
              <input
                type="number"
                bind:value={e.n}
                placeholder="N"
                class="w-full sm:w-16 border rounded px-2 py-1"
              />
              <button
                on:click={() => calcComp(e)}
                class="w-full sm:w-auto px-2 py-1 bg-blue-500 text-white rounded"
              >
                계산
              </button>
              <button
                on:click={() => remove(compEntries, e.id, v => compEntries = v)}
                class="w-full sm:w-auto px-2 py-1 bg-red-500 text-white rounded"
              >
                삭제
              </button>
              <div id={`resComp${e.id}`} class="text-sm mt-1 sm:mt-0"></div>
            </div>
          {/each}
        </div>
        <button
          on:click={addComp}
          class="w-full sm:w-auto px-4 py-2 bg-green-600 text-white rounded"
        >
          + 항목 추가
        </button>
        <div
          id="resultQ"
          class="mt-2 p-2 bg-gray-100 border-l-4 border-blue-500 text-sm"
        ></div>
      </fieldset>

      <!-- 2. 액화가스 -->
      <fieldset class="border rounded-lg p-4 space-y-4">
        <legend class="font-medium">2. 액화가스 저장량 (B, kg)</legend>
        <div class="text-gray-600 text-sm">B = 0.9·d·V₂·N</div>
        <div class="space-y-2">
          {#each liqEntries as e (e.id)}
            <div
              class="flex flex-col sm:flex-row sm:items-center sm:space-x-2 space-y-2 sm:space-y-0 border rounded p-2"
            >
              <input
                type="number"
                bind:value={e.d}
                placeholder="d (kg/L)"
                class="w-full sm:w-24 border rounded px-2 py-1"
              />
              <input
                type="number"
                bind:value={e.v2}
                placeholder="V₂ (L)"
                class="w-full sm:w-24 border rounded px-2 py-1"
              />
              <input
                type="number"
                bind:value={e.n}
                placeholder="N"
                class="w-full sm:w-16 border rounded px-2 py-1"
              />
              <button
                on:click={() => calcLiq(e)}
                class="w-full sm:w-auto px-2 py-1 bg-blue-500 text-white rounded"
              >
                계산
              </button>
              <button
                on:click={() => remove(liqEntries, e.id, v => liqEntries = v)}
                class="w-full sm:w-auto px-2 py-1 bg-red-500 text-white rounded"
              >
                삭제
              </button>
              <div id={`resLiq${e.id}`} class="text-sm mt-1 sm:mt-0"></div>
            </div>
          {/each}
        </div>
        <button
          on:click={addLiq}
          class="w-full sm:w-auto px-4 py-2 bg-green-600 text-white rounded"
        >
          + 항목 추가
        </button>
        <div
          id="resultB"
          class="mt-2 p-2 bg-gray-100 border-l-4 border-blue-500 text-sm"
        ></div>
      </fieldset>

      <!-- 3. 용기 저장량 -->
      <fieldset class="border rounded-lg p-4 space-y-4">
        <legend class="font-medium">3. 용기 저장량 (C, kg)</legend>
        <legend class="font-medium"><strong>가스별 상수값 (C)</strong></legend>
        <div class="text-gray-600 text-sm">
          액화산소: 1.04, 액화질소: 1.47, 액화아르곤: 0.87, 액화암모니아: 1.86, 액화이산화탄소: 1.47
        </div>
        <div class="text-gray-600 text-sm">C = (V₂ ÷ C)·N</div>
        <div class="space-y-2">
          {#each ctnEntries as e (e.id)}
            <div
              class="flex flex-col sm:flex-row sm:items-center sm:space-x-2 space-y-2 sm:space-y-0 border rounded p-2"
            >
              <input
                type="number"
                bind:value={e.v2}
                placeholder="V₂ (L)"
                class="w-full sm:w-24 border rounded px-2 py-1"
              />
              <input
                type="number"
                bind:value={e.c}
                placeholder="정수 C"
                class="w-full sm:w-24 border rounded px-2 py-1"
              />
              <input
                type="number"
                bind:value={e.n}
                placeholder="N"
                class="w-full sm:w-16 border rounded px-2 py-1"
              />
              <button
                on:click={() => calcCtn(e)}
                class="w-full sm:w-auto px-2 py-1 bg-blue-500 text-white rounded"
              >
                계산
              </button>
              <button
                on:click={() => remove(ctnEntries, e.id, v => ctnEntries = v)}
                class="w-full sm:w-auto px-2 py-1 bg-red-500 text-white rounded"
              >
                삭제
              </button>
              <div id={`resCtn${e.id}`} class="text-sm mt-1 sm:mt-0"></div>
            </div>
          {/each}
        </div>
        <button
          on:click={addCtn}
          class="w-full sm:w-auto px-4 py-2 bg-green-600 text-white rounded"
        >
          + 항목 추가
        </button>
        <div
          id="resultC"
          class="mt-2 p-2 bg-gray-100 border-l-4 border-blue-500 text-sm"
        ></div>
      </fieldset>
      <!-- <fieldset class="border rounded-lg p-4 space-y-4">
        <legend class="font-medium"><strong>가스별 상수값 (C)</strong></legend>
        <div class="text-gray-600 text-sm">
          액화산소: 1.04, 액화질소: 1.47, 액화아르곤: 0.87, 액화암모니아: 1.86, 액화이산화탄소: 1.47
        </div>
      </fieldset> -->
      <!-- 4~7. 독성 가스 -->
      <fieldset class="border rounded-lg p-4 space-y-4">
        <legend class="font-medium">4~7. 독성가스 저장량</legend>
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <label>독성 액화가스 저장량 W (kg): 
          <input
            id="w"
            type="number"
            placeholder="W (kg)"
            value="1"
            class="w-full border rounded px-2 py-1"
          />
        </label>
        <label>200ppm 이하 독성 액화가스 저장량 X (kg): 
          <input
            id="x"
            type="number"
            placeholder="X (kg)"
            value="1"
            class="w-full border rounded px-2 py-1"
          />
          </label>
          <label>독성 압축가스 저장량 Y (m³): 
          <input
            id="y"
            type="number"
            placeholder="Y (m³)"
            value="1"
            class="w-full border rounded px-2 py-1"
          />
          </label>
          <label>200ppm 이하 독성 압축가스 저장량 Z (m³): 
          <input
            id="z"
            type="number"
            placeholder="Z (m³)"
            value="1"
            class="w-full border rounded px-2 py-1"
          />
          </label>
        </div>
        <button
          on:click={calcToxic}
          class="w-full sm:w-auto px-4 py-2 bg-blue-600 text-white rounded"
        >
          독성 계산
        </button>
        <div
          id="resultToxic"
          class="mt-2 p-2 bg-gray-100 border-l-4 border-blue-500 text-sm"
        ></div>
      </fieldset>

      <!-- 허가대상 여부 -->
      <fieldset class="border rounded-lg p-4">
        <legend class="font-medium">허가대상 여부</legend>
        <div class="text-gray-600 text-sm mb-2">
          기준: (A/500)+(B/5000)+(C/5000)+(W/1000)+(X/100)+(Y/100)+(Z/10) ≥ 1
        </div>
        <button
          on:click={checkPerm}
          class="w-full sm:w-auto px-4 py-2 bg-red-600 text-white rounded"
        >
          판단하기
        </button>
        <div
          class="mt-2 p-2 bg-gray-100 border-l-4 border-blue-500 text-sm"
        >
          {permMsg}
        </div>
      </fieldset>
    </main>
  </Sidebar.Inset>
</Sidebar.Provider>