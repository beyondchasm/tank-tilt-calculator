<!-- src/routes/+layout.svelte -->
<script lang="ts" setup>
  import * as Sidebar from "$lib/components/ui/sidebar";
  import AppSidebar from "$lib/components/app-sidebar.svelte";
  import * as Breadcrumb from "$lib/components/ui/breadcrumb";
  import { Separator } from "$lib/components/ui/separator";
  import { onMount } from "svelte";
  import "../app.css";

  let deferredPrompt: any = null;
  let showInstall = false;

  onMount(() => {
    if (typeof window === "undefined") return;
    window.addEventListener("beforeinstallprompt", (e) => {
      e.preventDefault();
      deferredPrompt = e;
      showInstall = true;
    });
  });

  function installApp() {
    if (!deferredPrompt) return;
    deferredPrompt.prompt();
    deferredPrompt.userChoice.then(() => {
      deferredPrompt = null;
      showInstall = false;
    });
  }
</script>

<svelte:head>
  <link rel="manifest" href="/manifest.webmanifest" />
  <meta name="theme-color" content="#ffffff" />
  <link rel="apple-touch-icon" href="/pwa-192x192.png" />
</svelte:head>

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
            <Breadcrumb.Page>GasWorks Hub</Breadcrumb.Page>
          </Breadcrumb.Item>
        </Breadcrumb.List>
      </Breadcrumb.Root>
    </header>

    <main class="min-h-screen bg-white relative">
      <!-- Hero 섹션 -->
      <section class="flex flex-col items-center justify-center text-center bg-gradient-to-r from-slate-50 to-white py-20 px-6">
        <h1 class="text-4xl md:text-5xl font-bold mb-4">GasWorks Hub</h1>
        <p class="text-lg md:text-xl text-gray-600 mb-8">
          가스시공 현장의 모든 정보와 계산기를 한곳에.<br class="hidden md:inline"/>
          정확하고 빠른 시공을 위한 올인원 플랫폼
        </p>
      </section>

      <!-- 주요 기능 섹션 -->
      <section class="py-16 px-6">
        <div class="max-w-5xl mx-auto">
          <h2 class="text-3xl font-semibold text-center mb-10">주요 기능</h2>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
            <!-- 탱크 기울기 계산기 카드 -->
            <a
              href="/tank/mode1"
              sveltekit:prefetch
              class="block p-6 bg-white rounded-lg shadow hover:shadow-md transition cursor-pointer"
            >
              <h3 class="font-bold mb-2">탱크 기울기 계산기</h3>
              <p class="text-gray-600 text-sm">
                관측 각도와 높이/거리 입력만으로<br/>
                기울기 비율·수평거리·관측거리 계산
              </p>
            </a>

            <!-- 가스 저장량 계산기 카드 -->
            <a
              href="/gas-storage"
              sveltekit:prefetch
              class="block p-6 bg-white rounded-lg shadow hover:shadow-md transition cursor-pointer"
            >
              <h3 class="font-bold mb-2">가스 저장량 계산기</h3>
              <p class="text-gray-600 text-sm">
                압축·액화·용기·독성가스 저장량 산출<br/>
                허가 대상 여부까지 한 번에 확인
              </p>
            </a>

            <!-- 추가 기능 카드 여기에... -->
          </div>
        </div>
      </section>

      <!-- 각 페이지 렌더링 -->
      <div class="px-6 pb-12">
        <slot />
      </div>

      <!-- 푸터 -->
      <footer class="py-12 px-6 text-center">
        <p class="text-gray-500 mb-4">© 2025 GasWorks Hub. All rights reserved.</p>
      </footer>

      {#if showInstall}
        <button
          on:click={installApp}
          class="fixed bottom-8 right-8 bg-blue-600 text-white px-4 py-2 rounded shadow-lg hover:bg-blue-700 transition"
        >
          앱 설치하기
        </button>
      {/if}
    </main>
  </Sidebar.Inset>
</Sidebar.Provider>