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
									Sign up
									<strong>failed!</strong>
								</v-alert>
								<v-form>
									<v-text-field
										label="Username"
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
										:rules="pwdRules"
										v-model="password"
									></v-text-field>

									<v-text-field
										id="passwordConfirm"
										label="Confirm Password"
										name="passwordConfirm"
										prepend-icon="mdi-lock"
										type="password"
										:rules="pwdConfirm"
										v-model="passwordConfirm"
										@keyup.enter="signUpUser"
									></v-text-field>
								</v-form>
							</v-card-text>
							<v-card-actions>
								<v-spacer></v-spacer>
								<v-btn color="primary" @click.prevent="signUpUser"
									>Sign Up</v-btn
								>
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
	computed: {
		pwdRules: function() {
			return [(v) => !!v || 'Password required'];
		},
		pwdConfirm: function() {
			return [
				(v) => !!v || 'Confirm password',
				(v) => v === this.password || 'Passwords do not match',
			];
		},
	},
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
		async signUpUser() {
			try {
				let r = await this.postData(
					`${process.env.VUE_APP_API_URL}/users/new`,
					{
						username: this.username,
						password: this.password,
					}
				);
				if (r.username) {
					this.$emit('signedUp', r.username);
				}
			} catch (err) {
				console.log(err);
			}
		},
	},
};

// {
//     "_id": "5f0f51fa42788700176a576e",
//     "username": "lordlord",
//     "password": "$2a$10$A5WCHcbcSSZluR9alLx2heWjs2pORcuLJ8xZs9zEM.zGaRqp1ZJpO",
//     "createdAt": "2020-07-15T18:59:06.611Z",
//     "updatedAt": "2020-07-15T18:59:06.611Z",
//     "__v": 0
// }
</script>
