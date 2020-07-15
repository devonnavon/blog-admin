<template>
	<v-app id="inspire">
		<v-main>
			<v-container class="fill-height" fluid>
				<v-row align="center" justify="center">
					<v-col cols="12" sm="8" md="4">
						<v-card class="elevation-12">
							<v-toolbar color="primary" dark flat>
								<v-toolbar-title>Sign Up for the B L o G</v-toolbar-title>
								<v-spacer></v-spacer>
							</v-toolbar>
							<v-card-text>
								<v-alert v-if="authFailed" dense outlined type="error">
									Passwords don't
									<strong>match!</strong>
								</v-alert>
								<v-form>
									<v-text-field
										label="Sign Up"
										name="username"
										prepend-icon="mdi-account"
										type="text"
										v-model="username"
									></v-text-field>

									<v-text-field
										id="password"
										label="Password"
										name="password"
										prepend-icon="mdi-lock"
										type="password"
										v-model="password"
									></v-text-field>

									<v-text-field
										id="passwordConfirm"
										label="Confirm Password"
										name="passwordConfirm"
										prepend-icon="mdi-lock"
										type="password"
										v-model="passwordConfirm"
										@keyup.enter="loginUser"
									></v-text-field>
								</v-form>
							</v-card-text>
							<v-card-actions>
								<v-spacer></v-spacer>
								<v-btn color="primary">Sign Up</v-btn>
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
	name: 'SignUp',
	props: {
		source: String,
	},
	data: () => ({
		authFailed: false,
		username: null,
		password: null,
		passwordConfirm: null,
		error: {
			isError: false,
			message: "Passwords don't match!",
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
