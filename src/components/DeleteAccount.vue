<template>
  <div>
    <button class="openDelete" @click="openDeleteAccount = true">
      Delete Account
    </button>
    <div class="delete" v-if="openDeleteAccount === true">
      <section id="deleteContainer">
        <h3 v-if="accountDeleted">Your account was successfully deleted.</h3>
        <div>
          <p>Are you sure you want to delete your account?</p>
          <div id="deleteButtons">
            <button @click="inputPassword = !inputPassword">
              Delete
            </button>
            <button @click="openDeleteAccount = false">Nevermind</button>
          </div>
          <form action="javascript:void(0)" v-if="inputPassword">
            <input type="password" id="confirmPassword" placeholder="Please enter your password" />
           <br> 
            <input type="submit" @click="deleteAccount" />
          </form>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import cookies from "vue-cookies";
export default {
  name: "delete-account",
  data() {
    return {
      inputPassword: false,
      accountDeleted: false,
      openDeleteAccount: false,
    };
  },
  computed: {
    storeCurrentUser() {
      return this.$store.state.currentUser;
    },
  },

  methods: {
   
    mutateDeleteAccountOptions() {
      console.log(this.storeDeleteAccountOptions);

      this.$store.commit("updateDeleteAccountOptions", false);
    },
    deleteAccount() {
      axios
        .request({
          method: "DELETE",
          url: `${process.env.VUE_APP_API_KEY}/api/users`,
          headers: {
            "Content-Type": "application/json"
          },
          data: {
            loginToken: this.storeCurrentUser.loginToken,
            password: document.getElementById("confirmPassword").value,
          },
        })
        .then((res) => {
          console.log(res);
          this.accountDeleted = !this.accountDeleted;
         cookies.remove("currentUser");
      this.$store.commit("updateCurrentUser", null);
      this.$router.push("/welcome");
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style lang="scss" scoped>
// .deleteGrid {
//   position: absolute;
//   top: 0;
//   right: 0;
//   display: grid;
//   place-items: center;
//   width: 100%;
//   height: 100vh;
//   background: floralwhite;
// }
.openDelete {
  width: 180px;
  height: 30px;

  font-size: 12px;
  font-weight: 600;
  color: #829376;
  background: #d9dfcd;
  border-radius: 20px;
}
.delete {
  background: #829376;
  position: absolute;
  top: 0;
  left: 0;
  height: 100vh;
  width: 100%;
  // margin: 0 10vw;
  display: grid;
  place-items: center;
}
#deleteContainer {
  width: 70vw;
  height: 300px;
  background-color: #d9dfcd;
  display: grid;
  place-items: center;
  button {
    width: 80px;
    height: 20px;
 
  font-size: 12px;
    font-weight: 600;
    color:#d9dfcd;
    background:  #829376;
    border-radius: 10px;
    margin: 20px;
  } 
  &:hover {
color: #829376;
    background: #d9dfcd;
  } 
}
#confirmPassword {
  width: 170px;
}
</style>