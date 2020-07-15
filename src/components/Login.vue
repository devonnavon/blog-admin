<template>
	<v-app id="inspire">
		<v-main>
			<v-container class="fill-height" fluid>
				<v-row align="center" justify="center">
					<v-col cols="12" sm="8" md="4">
						<v-card class="elevation-12">
							<v-toolbar color="primary" dark flat>
								<v-toolbar-title>Login to the B L o G</v-toolbar-title>
								<v-spacer></v-spacer>
							</v-toolbar>
							<v-card-text>
								<v-alert v-if="authFailed" dense outlined type="error">
									Log in
									<strong>failed!</strong>
								</v-alert>
								<v-form>
									<v-text-field
										label="Login"
										name="username"
										prepend-icon="mdi-account"
										type="text"
										v-model="username"
										value="aasdasd"
									></v-text-field>

									<v-text-field
										id="password"
										label="Password"
										name="password"
										prepend-icon="mdi-lock"
										type="password"
										v-model="password"
										@keyup.enter="loginUser"
									></v-text-field>
								</v-form>
							</v-card-text>
							<v-card-actions>
								<v-spacer></v-spacer>
								<v-btn color="primary" @click.prevent="loginUser">Login</v-btn>
							</v-card-actions>
						</v-card>
					</v-col>
				</v-row>
			</v-container>
		</v-main>
	</v-app>
</template>

<script>
export default {
	name: 'Login',
	// props: {
	// 	username: String,
	// },
	data: () => ({
		authFailed: false,
		username: null,
		password: null,
		error: {
			isError: false,
			message: 'Invalid Login',
		},
	}),
	methods: {
		async postData(url = '', data = {}) {
			const response = await fetch(url, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify(data),
			});
			return response.json();
		},
		async loginUser() {
			try {
				let r = await this.postData(`${process.env.VUE_APP_API_URL}/login`, {
					username: this.username,
					password: this.password,
				});
				if (r.token) {
					localStorage.setItem('token', r.token);
					this.$emit('loggedIn');
				} else {
					this.authFailed = true;
				}
			} catch (err) {
				console.log(err);
			}
		},
	},
};
</script>
