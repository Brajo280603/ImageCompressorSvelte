<script>
	import '../app.postcss';
	import { AppShell, AppBar, LightSwitch } from '@skeletonlabs/skeleton';
</script>

<!-- App Shell -->
<AppShell>
	<svelte:fragment slot="header">
		<!-- App Bar -->
		<AppBar>
			<svelte:fragment slot="lead">
				<a
				class="btn m-0 p-0 variant-ghost-surface btn-icon me-2"
				href="https://github.com/Brajo280603/ImageCompressorNODE"
				target="_blank"
			>
				<img src="/github.svg" alt="" class="dark:invert">
			</a>
				<strong class="text-xl uppercase">image Compressor</strong>
			</svelte:fragment>
			<svelte:fragment slot="trail">
	

				<LightSwitch />
			</svelte:fragment>
		</AppBar>
	</svelte:fragment>
	<!-- Page Route Content -->
	<slot />
</AppShell>
