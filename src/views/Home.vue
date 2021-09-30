<template>
	<div>
		<h1>LISTA DE CLIENTES</h1>
		<div>
			<CustomerList />
		</div>
	</div>
</template>
<script>
	// @ is an alias to /src
	import CustomerList from '@/components/CustomerList.vue';

	export default {
		name: 'Home',
		components: {
			CustomerList,
		},
	};
</script>
