<!-- src/routes/tank/mode1/+page.svelte -->
<script lang="ts" setup>
    import * as Sidebar from "$lib/components/ui/sidebar";
    import AppSidebar from "$lib/components/app-sidebar.svelte";
    import * as Breadcrumb from "$lib/components/ui/breadcrumb";
    import { Separator } from "$lib/components/ui/separator";
    import { Button, buttonVariants } from "$lib/components/ui/button/index.js";
  



 


    import '../../../app.css';
  
    // 모드1 계산 변수
    let bDeg  = 3.4948;
    let bAz   = 7.1602;
    let cDeg  = 20.4192;
    let cAz   = 7.2121;
    let h     = 6.563;
    let result = '';
  
    const degToRad = (deg: number) => deg * Math.PI / 180;
  
    function calculateTiltFromHeight() {
        console.log('ssss');
      const B     = degToRad(bDeg);
      const C     = degToRad(cDeg);
      const D     = degToRad(Math.abs(cAz - bAz));
      const L     = h / (Math.tan(C) - Math.tan(B));
      const H1    = L * Math.tan(C);
      const E     = Math.hypot(L, H1);
      const delta = E * Math.tan(D);
      const tilt  = (delta / H1) * 100;
  
      result = `
  수평거리 L: ${L.toFixed(4)} m
  상단 높이 H₁: ${H1.toFixed(4)} m
  관측 거리 E: ${E.toFixed(4)} m
  수평 이탈 δ: ${delta.toFixed(4)} m
  기울기 비율: ${tilt.toFixed(4)} %
      `;
    }
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
              <Breadcrumb.Link href="/tank/mode1">
                Tank Tilt Calculator
              </Breadcrumb.Link>
            </Breadcrumb.Item>
            <Breadcrumb.Separator class="hidden md:block" />
            <Breadcrumb.Item>
              <Breadcrumb.Page>탱크 높이 기준 계산</Breadcrumb.Page>
            </Breadcrumb.Item>
          </Breadcrumb.List>
        </Breadcrumb.Root>
      </header>
  
      <main class="min-h-screen bg-white p-6">
        <div class="max-w-3xl mx-auto space-y-6 border mx-auto px-2 rounded-lg p-4 mb-8">
          <a href="/tank/mode2">👉🏻 수평 거리 기준 계산으로 이동</a>
        </div>
        <div class="max-w-3xl mx-auto space-y-6">
          
          <fieldset class="border rounded-lg p-4 space-y-4">
            <legend class="px-2 font-medium">모드 1: 탱크 높이 기준 계산</legend>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              <label class="flex flex-col">
                하단 수직각 (deg)
                <input type="number" bind:value={bDeg} step="0.0001"
                       class="border rounded px-2 py-1" />
              </label>
              <label class="flex flex-col">
                하단 수평각 (deg)
                <input type="number" bind:value={bAz} step="0.0001"
                       class="border rounded px-2 py-1" />
              </label>
              <label class="flex flex-col">
                상단 수직각 (deg)
                <input type="number" bind:value={cDeg} step="0.0001"
                       class="border rounded px-2 py-1" />
              </label>
              <label class="flex flex-col">
                상단 수평각 (deg)
                <input type="number" bind:value={cAz} step="0.0001"
                       class="border rounded px-2 py-1" />
              </label>
              <label class="flex flex-col sm:col-span-2">
                탱크 높이 H (m)
                <input type="number" bind:value={h} step="0.0001"
                       class="border rounded px-2 py-1" />
              </label>
            </div>
  
            
            <Button >
                <buttonVariants on:click={calculateTiltFromHeight}>계산하기</buttonVariants>
            </Button>
          </fieldset>
  
          {#if result}
          <div class="max-w-3xl mx-auto space-y-6">
            <fieldset class="border rounded-lg p-4 space-y-4">
              <legend class="px-2 font-medium">결과</legend>
              <pre class="bg-gray-50
              font-mono whitespace-pre-wrap text-sm">
            {result}
            </pre>
              </div>

          {/if}
        </div>
      </main>
    </Sidebar.Inset>
  </Sidebar.Provider>