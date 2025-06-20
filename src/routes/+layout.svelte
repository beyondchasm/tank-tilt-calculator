<script lang="ts">
	import * as Sidebar from "$lib/components/ui/sidebar/index.js";
	let { children } = $props();
  </script>
  
  <!-- 여기에 메타/매니페스트 태그 넣기 -->
  <svelte:head>
	<!-- PWA 매니페스트 -->
	<link rel="manifest" href="/manifest.webmanifest" />
  
	<!-- 테마 컬러 (주소창 색 등) -->
	<meta name="theme-color" content="#ffffff" />
  
	<!-- iOS 홈스크린 아이콘 -->
	<link rel="apple-touch-icon" href="/pwa-192x192.png" />
  </svelte:head>
  
  <Sidebar.Provider>
	<main style="width: 100%;">
	  {@render children?.()}
	</main>
  </Sidebar.Provider>