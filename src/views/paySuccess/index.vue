<!-- 组件说明 -->
<template>
	<div class="main">
		<div class="pay-success">
			<steps/>
			<successTxt/>
			
		</div>
	</div>
</template>

<script>
	import steps from './steps.vue'
	import successTxt from './successTxt.vue'
	
  export default {
    name: '',
    
    data() {
      return {}
    },
    
    props: {},
    
    components: {
      steps,
      successTxt
    },
    
    computed: {},
    
    methods: {}
  }
</script>

<style scoped lang="scss">
	
	.pay-success{
		width: 1200px;
		margin: 0 auto;
	}
</style>