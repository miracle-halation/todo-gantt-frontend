<template>
	<v-card>
		<v-img
      src="https://cdn.vuetifyjs.com/images/cards/road.jpg"
      height="300px"
    ></v-img>
		<v-card-title>
			新規登録
		</v-card-title>
		<ValidationObserver
			ref="observer"
			v-slot="{ invalid }"
		>
			<form @submit.prevent="submit">
				<ValidationProvider
					v-slot="{ errors }"
					name="name"
					rules="required"
				>
					<v-text-field
						v-model="name"
						:error-messages="errors"
						label="名前"
						required
					></v-text-field>
				</ValidationProvider>

				<ValidationProvider
					v-slot="{ errors }"
					name="email"
					rules="required|email"
				>
					<v-text-field
						v-model="email"
						:error-messages="errors"
						label="メールアドレス"
						required
					></v-text-field>
				</ValidationProvider>

				<ValidationProvider
					v-slot="{ errors }"
					name="Password"
					rules="required|max:20|confirmed:PasswordConfirm"
				>
					<v-text-field
						v-model="password"
						:counter="20"
						:error-messages="errors"
						:append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
						:type="showPassword ? 'text' : 'password'"
						label="パスワード"
						required
						@click:append="showPassword = !showPassword"
					></v-text-field>
				</ValidationProvider>

				<ValidationProvider
					v-slot="{ errors }"
					name="PasswordConfirm"
					rules="required|max:20"
				>
					<v-text-field
						v-model="password_confirm"
						:counter="20"
						:error-messages="errors"
						:append-icon="showPasswordConfirm ? 'mdi-eye' : 'mdi-eye-off'"
						:type="showPasswordConfirm ? 'text' : 'password'"
						label="パスワード確認"
						required
						@click:append="showPasswordConfirm = !showPasswordConfirm"
					></v-text-field>
				</ValidationProvider>

				<v-btn
					class="mr-4"
					type="submit"
					:disabled="invalid"
					@click="handleSignup"
				>
					submit
				</v-btn>
			</form>
		</ValidationObserver>
		<v-card-actions>
      <v-btn
        color="orange lighten-2"
        text
      >
				<nuxt-link to='/login'>ログイン</nuxt-link>
			</v-btn>
		</v-card-actions>
	</v-card>
</template>

<script>
export default {
	name:'signup',
	data() {
		return {
			name: '',
			email: '',
			password: '',
			password_confirm: '',
			showPassword: false,
      showPasswordConfirm: false,
		}
	},
	methods:{
		async submit(){
			this.$refs.observer.validate()
		},
		async handleSignup(){
			const AuthData = new FormData();
			AuthData.append('email', this.email)
			AuthData.append('password', this.password)
			AuthData.append('name', this.name)
			await this.$axios.post('/v1/auth', AuthData)
			.then((response) => {
				const user_data = response.data.data
			}).catch((err) => {
				console.log(err)
			})
			await this.$auth.loginWith('local', {data: {email: this.email, password: this.password}})
			.then((response) => {
				const headers = {
					uid: response.headers['uid'],
					access_token: response.headers['access-token'],
					client: response.headers['client']
				}
			})
		},
	}
}
</script>

<style scoped>
	form{
		padding: 0 1rem 1rem 1rem
	}
</style>