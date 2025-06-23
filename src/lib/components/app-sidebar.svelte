<script lang="ts" module>
	// sample data
	const data = {
		versions: ["0.0.1"],
		navMain: [
			{
				title: "Home",
				url: "/home",
				items:[
					{
						title: "README",
						url: "/home/readme",
						isActive : true
					},
				]
			},
			{
				title: "Tank Tilt Calulotor",
				url: "/tank",
				items: [
					{
						title: "탱크 높이 기준 계산",
						url: "/tank/mode1",
						
					},
					{
						title: "수평 거리 기준 계산",
						url: "/tank/mode2",
					},
				],
			},
			{
				title: "Gas Storage Calulotor",
				url: "/gas-storage",
				items: [
					{
						title: "가스 저장량 계산기",
						url: "/gas-storage",
						
					},
			
				],
			},
		],
	};
</script>

<script lang="ts">
	import SearchForm from "./search-form.svelte";
	import VersionSwitcher from "./version-switcher.svelte";
	import * as Sidebar from "$lib/components/ui/sidebar/index.js";
	import type { ComponentProps } from "svelte";

	let { ref = $bindable(null), ...restProps }: ComponentProps<typeof Sidebar.Root> = $props();
</script>

<Sidebar.Root {...restProps} bind:ref>
	<Sidebar.Header>
		<VersionSwitcher versions={data.versions} defaultVersion={data.versions[0]} />
		<SearchForm />
	</Sidebar.Header>
	<Sidebar.Content>
		<!-- We create a Sidebar.Group for each parent. -->
		{#each data.navMain as group (group.title)}
			<Sidebar.Group>
				<Sidebar.GroupLabel>{group.title}</Sidebar.GroupLabel>
				<Sidebar.GroupContent>
					<Sidebar.Menu>
						{#each group.items as item (item.title)}
							<Sidebar.MenuItem>
								<Sidebar.MenuButton isActive={item.isActive}>
									{#snippet child({ props })} 
										<a href={item.url} {...props}>{item.title}</a>
									{/snippet}
								</Sidebar.MenuButton>
							</Sidebar.MenuItem>
						{/each}
					</Sidebar.Menu>
				</Sidebar.GroupContent>
			</Sidebar.Group>
		{/each}
	</Sidebar.Content>
	<Sidebar.Rail />
</Sidebar.Root>
