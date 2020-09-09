<template>
	<div id="page">
		<Nuxt />
	</div>
</template>

<style lang="scss">
	#page {
		background-color: $gray;
		margin-top: 68px;
		margin-left: 10px;
		margin-right: 10px;
		height: calc(100vh - 68px);

		@media (min-width: $first-breakpoint) {
			margin-left: 55px;
			margin-right: 0;
		}

		@media (max-width: $second-breakpoint) {
			margin-top: 10px;
			height: calc(100vh - 10px);
		}
	}
</style>
