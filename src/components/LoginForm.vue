<template>
  <div>
    <form action="javascript:void(0)">
      
      <input type="username" placeholder="username" id="enterUsername" autocomplete="" class="loginInput"/>

      <input
        type="password"
        name="password"
        placeholder="password"
        autocomplete=""
        id="loginPassword"
        class="loginInput"
      />
   
      <input class="loginInput" type="submit" value="login" name="submitNewUser" id="loginButton" @click="attemptLogin" />
      <h3>{{ loginStatus }}</h3>
    </form>
  </div>
</template>

<script>
import axios from "axios";
import cookies from "vue-cookies";
export default {
  name: "login-form",
  data() {
    return {
      loginStatus: "",
    };
  },
  computed: {
    storeCurrentUser() {
      return this.$store.state.currentUser;
    }
  },
  mounted () {
    if(this.storeCurrentUser === null) {
      console.log("please login");
    } else if (this.storeCurrentUser){ this.$router.push("/"); }
  },
  methods: {
    boxShadow: function(){
      document
    },
    attemptLogin: function () {
      axios
        .request({
          url: `${process.env.VUE_APP_API_KEY}/api/login`,
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            // "X-Api-Key": `${process.env.VUE_APP_API_KEY}`,
          },
          data: {
            username: document.getElementById("enterUsername").value,
            password: document.getElementById("loginPassword").value,
          },
        })

        .then((res) => {
          console.log(res.data);
          this.loginStatus = "you have logged in";
          cookies.set("currentUser", res.data);
          cookies.set("seeTweets", "friends");
          this.$store.commit("updateCurrentUser", res.data)
          this.$router.push("/");

        })
        // if the axios request fails, it will tell the user there was an error
        .catch((err) => {
          console.log(err);
          this.loginStatus = "your login attempt failed";
        });
    },
  },
};
</script>

<style lang="scss" scoped>
form {
  display: grid;
  place-items: center;
  gap: 30px;
  margin-top: -70px;
}
#loginButton {

  border-radius: 10px;
  width: 100px;
 
}
.loginInput {
  background-color: #d9dfcd;
  color: #000000;
  padding: 5px 10px;
  border: none;
  box-shadow: #505B4833 3px 3px 5px, #505B4833 -3px 3px 5px, #505B4833 3px -3px 5px, #505B4833 -3px -3px 4px;
 ::placeholder {
   color: #E8EEE3;
 }
}
@media screen and (min-width: 600px) {
  .loginInput {
    width: 140px;
  }
}

</style>