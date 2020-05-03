<template>
	<div id="regist" @keyup.enter="regist">
		<form novalidate autocomplete="off">
			<p style="margin-bottom:64px;color:#666;">test，test<a style="color:#f44336;font-size: 16px;" @click="goLogin">login</a></p>
			<md-avatar class="md-large">
				<img src="./img/regist_avatar.jpg" alt="People">
			</md-avatar>
			<md-input-container md-theme="whiteForm">
				<md-icon>
					<i class="iconfont icon-user"></i>
					<md-tooltip>icon</md-tooltip>
				</md-icon>
				<label></label>
				<md-input type="text" v-model="username" :required='inputFlag'></md-input>
				<md-icon></md-icon>
			</md-input-container>
			<md-input-container md-theme="whiteForm">
				<md-icon>
					<i class="iconfont icon-password"></i>
					<md-tooltip>password</md-tooltip>
				</md-icon>
				<label></label>
				<md-input :type="isHide ? 'password' : ''" v-model="password" :required='inputFlag'></md-input>
				<md-icon></md-icon>
			</md-input-container>
			<md-button class="md-raised md-warn" md-theme="whiteForm" @click="regist">register</md-button>
		</form>
		<md-dialog-alert :md-content="alert.content" :md-ok-text="alert.ok" @open="onOpen" @close="onClose" ref="check">
		</md-dialog-alert>
		<div class="overlay" v-show="registing">
			<md-spinner :md-size="150" md-indeterminate class="md-accent" md-theme="whiteForm"></md-spinner>
		</div>
	</div>
</template>
<script>
	import AV from "../../assets/js/av"
	import Util from "../../util/util"
	import "../../assets/js/initLeanCloud"
	export default {
		data() {
			return {
				alert: {
					content: ' ',
					ok: 'okay'
				},
				isHide: false,
				tipFlag: false,
				inputFlag: true,
				username: '',
				password: '',
				registing: false //注册中
			}
		},
		computed: {
		},
		mounted() {
			this.tipFlag = true;
			setTimeout(function() {
				this.tipFlag = false;
			}.bind(this), 4000)
		},
		methods: {
			regist() {
				var username = this.username;
				var pass = this.password;
				/*验证用户名和密码是否为空*/
				if (!this.check({
						username: username,
						pass: pass
					})) return;
				this.registing = true
				var user = new AV.User();
				user.setUsername(username);
				user.setPassword(pass);
				user.signUp().then(function(loginedUser) {
					this.registing = false;
					this.doneRegist();
					setTimeout(function() {
						this.$router.push({
							name: 'movie'
						})
					}.bind(this), 600)
				}.bind(this), (function(error) {
					if (error.code = '202') {
						this.alert.content = 'error';
					} else {
						this.alert.content = 'error, good';
					}
					this.openDialog('check');
					this.registing = false;
				}.bind(this)));
			},
			isEmpty(val) {
				return val === '';
			},
			isValidUserName(val) {
				return /^[a-zA-Z0-9_]+$/.test(val);
			},
			isValidPassword(val) {
				return /^[A-Za-z0-9_-]{4,}$/.test(val);
			},
			check(obj) {
				if (this.isEmpty(obj.username)) {
					this.alert.content = 'validate';
					this.openDialog('check');
					return false;
				}
				if (this.isEmpty(obj.pass)) {
					this.alert.content = 'no input';
					this.openDialog('check');
					return false;
				}
				if (!this.isValidUserName(obj.username)) {
					this.alert.content = 'not validate';
					this.openDialog('check');
					return false;
				}
				if (!this.isValidPassword(obj.pass)) {
					this.alert.content = 'not validate';
					this.openDialog('check');
					return false;
				}
				return true
			},
			doneRegist() {
				this.alert.content = 'registered';
				this.alert.ok = '';
				this.openDialog('check');
			},
			openDialog(ref) {
				this.$refs[ref].open();
			},
			closeDialog(ref) {
				this.$refs[ref].close();
			},
			onOpen() {
				console.log('Opened');
			},
			onClose(type) {
				console.log('Closed', type);
			},
			goLogin() {
				this.$router.push({
					name: 'login'
				});
			}
		}
	}
</script>
<style lang="scss" scoped>
	#regist {
		width: 100%;
		height: 100%;
		overflow: hidden;
		display: flex;
		justify-content: center;
		align-items: center;
		background: #fff;
		background: url('./img/regist_bg.jpg') no-repeat center center;
		background-size: cover;
		form {
			width: 70%;
		}
	}
	.md-theme-whiteForm.md-input-container.md-input-focused input {
		text-shadow: 0 0 0 rgba(255, 255, 255, .87);
	}
	.md-input {
		color: #fff;
		box-sizing: border-box;
		padding-left: 30px;
	}
	.md-input:focous {
		color: #fff;
	}
	.md-icon {
		position: absolute;
		left: 0
	}
	.iconfont {
		color: rgba(255, 255, 255, .84);
	}
	.md-input-container input {
		color: rgba(255, 255, 255, .84);
	}
	.md-input-container:after {
		background-color: #fff;
	}
	.md-avatar.md-large {
		width: 150px;
		min-width: 150px;
		height: 150px;
		min-height: 150px;
		border-radius: 150px;
	}
	.overlay {
		display: flex;
		justify-content: center;
		align-items: center;
		position: fixed;
		width: 100%;
		height: 100%;
		background-color: rgba(200, 200, 200, .6)
	}
	.md-spinner {
		position: fixed;
	}
	.login-tip {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		min-height: 64px;
		line-height: 64px;
		text-align: center;
		background-color: rgba(200, 200, 200, .8);
		color: #fff;
	}
	.slideT-enter-active,
	.slideT-leave-active {
		transition: all .5s
	}
	.slideT-enter-active {
		transform: translateY(0);
	}
	.slideT-enter,
	.slideT-leave-active {
		transform: translateY(-100%);
	}
</style>