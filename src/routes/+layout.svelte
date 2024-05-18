<script>
	import '../app.css';
	import '../app.postcss';
	import { AppShell, AppBar } from '@skeletonlabs/skeleton';
	import img from '$lib/fapollo_layout.png';

</script>

<!-- App Shell -->
<AppShell>
	<svelte:fragment slot="header">
		<!-- App Bar -->
		<AppBar gridColumns="grid-cols-1" slotDefault="place-self-center" slotTrail="place-content-end">
			<img class= "h-16" src={img}>
		</AppBar>
	</svelte:fragment>
	<!-- Page Route Content -->
	<slot />
	<svelte:fragment slot="pageFooter">
		<AppBar gridColumns="grid-cols-3" slotDefault="place-self-center" slotTrail="place-content-end">
			
		<svelte:fragment slot="lead">
				<div class="mt-4">
				All Rights Reserved, all files on this site are the <br>
				 property of their respective and rightful owners. 
				</div>
		</svelte:fragment>
			<div class="mt-4">Copyright © Fapollo.com All Rights Reserved</div>
			<svelte:fragment slot="trail">
				<div class="mt-4">
                    Disclaimer: This site does not store any files on its server.<br>
					 All contents are provided by non-affiliated third parties.
                </div>
			</svelte:fragment>

			
		</AppBar>
	</svelte:fragment>
</AppShell>
