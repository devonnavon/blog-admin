<template>
	<v-app>
		<v-main>
			<div v-if="!isFetching">
				<v-app-bar app>
					<span class="hidden-sm-and-up"></span>
					<v-toolbar-title>{{ appTitle }}</v-toolbar-title>
					<v-spacer></v-spacer>
					<v-toolbar-items class="hidden-xs-only">
						<v-btn
							text
							v-for="item in menuItems"
							:key="item.title"
							@click="menuClick(item.slug)"
						>
							<v-icon left dark>{{ item.icon }}</v-icon>
							{{ item.title }}
						</v-btn>
					</v-toolbar-items>
				</v-app-bar>
				<div v-if="selectedPage === 'login'">
					<Login @loggedIn="login" />
				</div>
				<div v-if="selectedPage === 'sign_up'">
					<SignUp @signedUp="signUpComplete" />
				</div>
				<div v-if="selectedPage === 'home'">
					<v-card
						class="mx-auto mt-3"
						max-width="600"
						outlined
						v-for="blog in blogs"
						:key="blog._id"
					>
						<v-list-item three-line>
							<v-list-item-content>
								<v-card
									class="d-inline-flex justify-space-between align-items-center align-content-center mb-2"
								>
									<span class="overline pl-2">Blog Post</span>
									<v-icon
										class="mr-1"
										v-show="!editPostToggle[blog._id]"
										@click="editPost(blog._id)"
										>mdi-one-up</v-icon
									>
									<v-icon
										class="mr-1"
										v-show="editPostToggle[blog._id]"
										@click="submitEdits(blog._id)"
										>mdi-check</v-icon
									>
									<v-icon class="mr-1" @click="deletePost(blog._id)"
										>delete</v-icon
									>
								</v-card>

								<v-list-item-title
									v-bind="editContent(blog._id)"
									v-bind:style="
										editPostToggle[blog._id]
											? 'border: 1px dotted orange;'
											: 'border: none;'
									"
									class="headline mb-1"
									@input="titleChange($event, blog._id)"
								>
									{{ blog.title }}
								</v-list-item-title>
								<v-list-item-subtitle
									class="mt-2"
									v-bind="editContent(blog._id)"
									@input="bodyChange($event, blog._id)"
									v-bind:style="
										editPostToggle[blog._id]
											? 'border: 1px dotted orange;'
											: 'border: none;'
									"
								>
									{{ blog.body }}
								</v-list-item-subtitle>
								<div v-show="commentsToggle[blog._id]">
									<v-card
										class
										max-width="450"
										outlined
										v-for="comment in getPostComments(blog._id)"
										:key="comment._id"
									>
										<v-list-item one-line>
											<v-list-item-content>
												<span class="font-weight-normal">{{
													comment.text
												}}</span>
											</v-list-item-content>
										</v-list-item>
										<v-card-actions>
											<v-row align="center" justify="space-between">
												<span class="ml-5 font-italic">
													{{ comment.user }}
												</span>
												<v-icon
													small
													class="mr-2"
													@click="deleteComment(comment._id)"
													>mdi-delete</v-icon
												>
											</v-row>
										</v-card-actions>
									</v-card>
								</div>

								<v-card-actions class="d-inline-flex flex-row-reverse">
									<v-btn text @click="toggleComments(blog._id)">Comments</v-btn>
									<v-btn
										text
										v-if="blog.published"
										@click="switchStatus(blog, false)"
										>Unpublish</v-btn
									>
									<v-btn text v-else @click="switchStatus(blog, true)"
										>Publish</v-btn
									>
								</v-card-actions>
							</v-list-item-content>
						</v-list-item>
					</v-card>
					<v-card max-width="600" class="mx-auto my-2">
						<v-card-actions>
							<v-icon big class="mx-auto" @click="newPost()"
								>mdi-plus-circle</v-icon
							>
						</v-card-actions>
					</v-card>
				</div>
				<div v-if="selectedPage === 'logout'">log out bro</div>
			</div>
			<div v-else>loading...</div>
		</v-main>
	</v-app>
</template>

<script>
import Login from './components/Login';
import SignUp from './components/SignUp';

export default {
	name: 'App',

	components: {
		Login,
		SignUp,
	},

	mounted() {
		this.checkToken();
		this.getPosts();
		this.getComments();
		this.isFetching = false;
	},
	data: () => ({
		isFetching: true,
		appTitle: 'B L o G',
		blogs: [],
		loggedIn: false,
		sidebar: false,
		comments: [],
		commentsToggle: {},
		editPostToggle: {},
		postEdits: {},
		selectedPage: 'login',
		defaultMenu: [
			{ title: 'Login', path: '', icon: 'lock_open', slug: 'login' },
			{ title: 'Sign Up', path: '', icon: 'face', slug: 'sign_up' },
		],
		authMenu: [
			{ title: 'Home', path: '', icon: 'home', slug: 'home' },
			{ title: 'Log Out', path: '', icon: 'logout', slug: 'logout' },
		],
		activeBorder: [],
	}),
	computed: {
		menuItems: function() {
			if (this.loggedIn) return this.authMenu;
			else return this.defaultMenu;
		},
		// borderState: function() {
		// 	return { activeBorder: '1px dotted gray' };
		// },
	},
	methods: {
		// editBorder(id) {
		// 	if (this.editBorder[id]) {
		// 		return { border: '1px dotted gray' };
		// 	}
		// },
		editContent(id) {
			if (this.editPostToggle[id]) {
				return { contenteditable: true };
			} else return { contenteditable: false };
		},
		titleChange(e, id) {
			this.postEdits[id].title = e.target.innerHTML;
		},
		bodyChange(e, id) {
			// console.log(this.postEdits[id]);
			// console.log(e.target.innerHTML);
			this.postEdits[id].body = e.target.innerHTML;
		},
		getPostComments(id) {
			let chill = this.comments.filter((e) => e.post === id);
			return chill;
		},
		login() {
			this.loggedIn = true;
			this.selectedPage = 'home';
		},
		logout() {
			this.loggedIn = false;
			localStorage.clear();
		},
		checkToken() {
			if (localStorage.token) {
				this.loggedIn = true;
				this.selectedPage = 'home';
			}
		},
		signUpComplete(username) {
			this.selectedPage = 'login';
			console.log(username);
		},
		menuClick(slug) {
			if (slug === 'logout') {
				this.selectedPage = 'login';
				this.logout();
			} else this.selectedPage = slug;
		},
		findBlog(id) {
			return this.blogs.findIndex((e) => e._id === id);
		},
		findComment(id) {
			return this.comments.findIndex((e) => e._id === id);
		},
		toggleComments(id) {
			this.$set(this.commentsToggle, id, !this.commentsToggle[id]);
		},
		async switchStatus(blog, status) {
			try {
				let r = await this.putData(
					`${process.env.VUE_APP_API_URL}/posts/${blog._id}`,
					{
						title: blog.title,
						body: blog.body,
						published: status,
					}
				);
				this.blogs.splice(this.findBlog(blog._id), 1, r);
			} catch (err) {
				console.log(err);
			}
		},
		async updatePost(id, title, body) {
			try {
				let r = await this.putData(
					`${process.env.VUE_APP_API_URL}/posts/${id}`,
					{
						title: title,
						body: body,
						published: this.blogs[this.findBlog(id)].published,
					}
				);
				this.blogs.splice(this.findBlog(id), 1, r);
			} catch (err) {
				console.log(err);
			}
		},
		async getPosts() {
			try {
				let r = await fetch(`${process.env.VUE_APP_API_URL}/posts`);
				this.blogs = await r.json();
				this.blogs.forEach((e) => {
					this.$set(this.commentsToggle, e._id, false);
					this.$set(this.editPostToggle, e._id, false);
					this.$set(this.postEdits, e._id, { title: e.title, body: e.body });
				}); //init commentsToggle to false so all comments are hidden
			} catch (err) {
				console.log(err);
			}
		},
		async getComments() {
			try {
				const r = await fetch(`${process.env.VUE_APP_API_URL}/comments/`);
				const commentsResponse = await r.json();

				this.comments = await commentsResponse;
			} catch (err) {
				console.log(err);
			}
		},
		async putData(url = '', data = {}) {
			const response = await fetch(url, {
				method: 'PUT',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify(data),
			});
			return response.json();
		},

		async deleteData(url = '') {
			const response = await fetch(url, {
				method: 'DELETE',
				// headers: {
				//   "Content-Type": "application/json"
				// },
			});
			return response.json();
		},
		async deleteComment(id) {
			try {
				await this.deleteData(`${process.env.VUE_APP_API_URL}/comments/${id}`);
				this.comments.splice(this.findComment(id), 1);
			} catch (err) {
				console.log(err);
			}
		},
		async deletePost(id) {
			try {
				await this.deleteData(`${process.env.VUE_APP_API_URL}/posts/${id}`);
				this.blogs.splice(this.findBlog(id), 1);
			} catch (err) {
				console.log(err);
			}
		},
		async postData(url = '', authToken, data = {}) {
			const response = await fetch(url, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
					Authorization: `Bearer ${authToken}`,
				},
				body: JSON.stringify(data),
			});
			return response.json();
		},
		async newPost() {
			try {
				let r = await this.postData(
					`${process.env.VUE_APP_API_URL}/posts/`,
					localStorage.token,
					{
						title: 'Post Title',
						body: 'Post Content',
						published: false,
					}
				);
				this.$set(this.blogs, this.blogs.length, r);
			} catch (err) {
				console.log(err);
			}
		},
		editPost(id) {
			this.$set(this.editPostToggle, id, true);
		},

		submitEdits(id) {
			this.updatePost(id, this.postEdits[id].title, this.postEdits[id].body);
			this.$set(this.editPostToggle, id, false);
		},
	},
};
</script>
