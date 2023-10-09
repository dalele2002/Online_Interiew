<template>
	<div class="login-page">
		<el-switch v-model="loginType"></el-switch>
		<el-card class="box-card" v-if="loginType">
			<div slot="header" class="clearfix">
				<span class="login-title">🔐Online - Interview</span>
			</div>
			<div class="login-form">
				<el-form :model="loginForm" :rules="loginRules" ref="loginForm">
					<el-form-item prop="username">
						<el-input type="text" v-model="loginForm.username" auto-complete="on" placeholder="请输入用户名"
							name="username" tabindex="1" ref="username">
							<template slot="prepend"><i style="font-size:20px" class="el-icon-user"></i></template>
						</el-input>
					</el-form-item>
					<el-form-item prop="password">
						<el-input v-model="loginForm.password" placeholder="请输入密码" name="password" tabindex="2"
							auto-complete="on" @keyup.enter.native="handleLogin" :key="passwordType" :type="passwordType"
							ref="password">
							<template slot="prepend"><i style="font-size:20px" class="el-icon-key"></i></template>
						</el-input>
						<span class="show-pwd" @click="showPwd">
							<svg-icon :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'" />
						</span>
					</el-form-item>
					<el-form-item>
						<el-button style="width:100%;" type="primary" @click="handleLogin" :loading="loading">登录</el-button>
					</el-form-item>
				</el-form>
			</div>
		</el-card>
	</div>
</template>

<script>
import { validUsername } from '@/utils/validate'

export default {
	name: 'Login',
	data() {
		const validateUsername = (rule, value, callback) => {
			if (!validUsername(value)) {
				callback(new Error('Please enter the correct user name'))
			} else {
				callback()
			}
		}
		const validatePassword = (rule, value, callback) => {
			if (value.length < 6) {
				callback(new Error('The password can not be less than 6 digits'))
			} else {
				callback()
			}
		}
		return {
			loginType: true,
			loginForm: {
				username: 'admin',
				password: '111111'
			},
			loginRules: {
				username: [{ required: true, trigger: 'blur', validator: validateUsername }],
				password: [{ required: true, trigger: 'blur', validator: validatePassword }]
			},
			loading: false,
			passwordType: 'password',
			redirect: undefined
		}
	},
	watch: {
		$route: {
			handler: function (route) {
				this.redirect = route.query && route.query.redirect
			},
			immediate: true
		}
	},
	methods: {
		showPwd() {
			if (this.passwordType === 'password') {
				this.passwordType = ''
			} else {
				this.passwordType = 'password'
			}
			this.$nextTick(() => {
				this.$refs.password.focus()
			})
		},
		handleLogin() {
			this.$refs.loginForm.validate(valid => {
				if (valid) {
					this.loading = true
					this.$store.dispatch('user/login', this.loginForm).then(() => {
						// this.$refs['loginForm'].$el.submit();//会打印密码到状态栏
						// this.$router.push({ path: this.redirect || '/' })
						this.$router.push({ name: 'createInterview' })
						this.loading = false
					}).catch(() => {
						this.loading = false
					})
				} else {
					console.log('error submit!!')
					return false
				}
			})
		}
	}
}
</script>

<style lang="scss" scoped>
.login-page {
	background-image: linear-gradient(180deg, #2af598 0%, #009efd 100%);
	height: 100vh;
	display: flex;
	justify-content: center;
	align-items: center;
}

.login-title {
	font-size: 20px;
}

.box-card {
	width: 375px;
	border-radius: 3%;
	transition: all 0.5s;
}
.box-card:hover {
    margin-top: -20px;
}
.show-pwd {
	position: absolute;
	right: 10px;
	color: #889aa4;
	cursor: pointer;
	user-select: none;
}
</style>
