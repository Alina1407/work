<template>
  <section>
  	{{ mag }}
  </section>
</template>
<script type="text/javascript">
export default {
	data() {
    	return {
    		mag:"sds"
    	}
	},
	created(){
		this.$axios.get("/movie/top250")
		.then(res=>{
			console.log(res)
		})
		.catch(err=>{
			console.log(err)
		})
	},
	methods: {

	},

}
</script>