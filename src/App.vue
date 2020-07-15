<template>
	<v-app>
		<v-main>
			<div v-if="loggedIn">
				Yo
			</div>
			<div v-else>
				<Login @loggedIn="login" />
			</div>
		</v-main>
	</v-app>
</template>

<script>
import Login from './components/Login';

export default {
	name: 'App',

	components: {
		Login,
	},

	data: () => ({
		blogs: [],
		loggedIn: false,
	}),

	methods: {
		login() {
			this.loggedIn = true;
			console.log('hey');
			console.log(this.loggedIn);
		},
	},
};
</script>
