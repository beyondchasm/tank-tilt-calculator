<script lang="ts">
	import * as Sidebar from "$lib/components/ui/sidebar/index.js";
	
	let { children } = $props();
   </script>	
   <Sidebar.Provider>
	<main style="width: 100%;">
		{@render children?.()}
	</main>

   </Sidebar.Provider>