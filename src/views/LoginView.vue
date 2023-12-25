<template>	
  <div class="mycombonent">
	<div class="qor">  
			<div v-if="!loggedIn" class="login">
				<form>
					<label>Login</label>
					<input v-model="username" type="text" name="username" placeholder="username" required="">
					<input v-model="password" type="password" name="password" placeholder="Password" required="">
					<button @click="login">Login</button>
				</form>
				<p :style="{ color: messageColor }">{{ message }}</p>
			</div>
			<div v-if="loggedIn">
				<router-link :to="{ name: 'home', }"></router-link>	
			</div>
	</div>	
  </div>
	
</template>

<script>
export default {
  data() {
    return {
      username: '',
      password: '',
      message: '',
      messageColor: '',
	  loggedIn: false
    };
  },
  methods: {
    login() {
      if (this.username === 'admin' && this.password === 'password') {
			this.messageColor = 'green';
			this.message = 'Login successful. Redirecting...';
			this.loggedIn = true;
			this.$router.push({ name: 'home' });
      } 
	  
	  else {
		if(this.username != '' || this.password != ''){
			this.messageColor = 'red';
        	this.message = 'Invalid username or password. Please try again.';
		}
      }
    },

  },
};
</script >

<style>
 .mycombonent{
	margin: 0;
	padding: 0;
	display: flex;
	justify-content: center;
	align-items: center;
	min-height: 100vh;
	font-family: 'Jost', sans-serif;
	background: linear-gradient(to bottom, #0f0c29, #302b63, #24243e);
} 
 .qor{
	width: 450px;
	height: 500px;
	background: linear-gradient(to bottom, #0f0c29, #302b63, #24243e);
	border-radius: 10px;
	box-shadow: 5px 20px 50px #000;
} 

.qor label{
	color: #fff;
	font-size: 2.3em;
	justify-content: center;
	display: flex;
	margin: 60px;
	font-weight: bold;
	cursor: pointer;
	transition: .5s ease-in-out;
}
.qor input{
	width: 60%;
	height: 50px;
	background: #e0dede;
	justify-content: center;
	display: flex;
	margin: 20px auto;
	padding: 10px;
	border: none;
	outline: none;
	border-radius: 5px;
}
.qor button{
	width: 60%;
	height: 40px;
	margin: 10px auto;
	justify-content: center;
	display: block;
	color: #fff;
	background: #354152;
	font-size: 1em;
	font-weight: bold;
	margin-top: 20px;
	outline: none;
	border: none;
	border-radius: 5px;
	transition: .2s ease-in;
	cursor: pointer;
}
.qor button:hover{
	background: #6d44b8;
}
.login{
	height: 460px;
	background: #eee;
	border-radius: 60% / 10%;
	transform: translateY(-180px);
	transition: .8s ease-in-out;
}
.login label{
	color: #573b8a;
}


</style>