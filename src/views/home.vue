<template>
  	<div class="home">
  	  	<HomePage/>
  	</div>
</template>

<script>
	import HomePage from '@/components/home-page.vue'

	export default {
	  	name: 'home',
	  	components: {
	  	  	HomePage
	  	}
	}
</script>

<style lang="scss">
	.home {
		height: 100%;
	}
</style>
