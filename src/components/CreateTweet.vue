<template>
  <div>
    <div id="ideaBox">
      <img
        class="idea"
        src="../assets/create.png"
        alt="create tweet"
        @click="createAnewTweet = true"
      />
    </div>
    <section v-if="createAnewTweet === true">
      <div class="goBack" @click="createAnewTweet = false">
        <img src="../assets/backArrow.svg" alt="back arrow" />
      </div>
      <form action="javascript:void(0)" autocomplete="off">
        <input type="text" id="tweetContent" placeholder="tweet content" />
        <input type="text" id="userImage" placeholder="image url" />
        <input
          id="submitNewTweet"
          type="submit"
          value="Post tweet!"
          @click="createTweet"
        />
      </form>
      <!-- @click="createTweet" -->
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "create-tweet",
  data() {
    return {
      createAnewTweet: false,
    };
  },
  computed: {
    storeCurrentUser() {
      return this.$store.state.currentUser;
    },
  },
  methods: {
    check: function () {
      console.log(
        document.getElementById("tweetContent").value +
          this.storeCurrentUser.loginToken
      );
    },
    createTweet: function () {
      axios
        .request({
          method: "POST",
          url: `${process.env.VUE_APP_API_KEY}/api/tweets`,
          headers: {
            "Content-Type": "application/json",
            // "X-Api-Key": `${process.env.VUE_APP_API_KEY}`,
          },
          data: {
            content: document.getElementById("tweetContent").value,
            imageUrl: document.getElementById("userImage").value,
            loginToken: this.storeCurrentUser.loginToken,
          },
        })
        .then((res) => {
          console.log(res.data + " create tweet");
          this.createAnewTweet = false;
          this.getAllTweets();
        })
        .catch((err) => {
          console.log(err);
        });
    },
    getAllTweets: function () {
      axios
        .request({
          method: "GET",
          url: `${process.env.VUE_APP_API_KEY}/api/tweets`,
          headers: {
            "Content-Type": "application/json",
            // "X-Api-Key": `${process.env.VUE_APP_API_KEY}`,
          },
        })
        .then((res) => {
          // console.log(res);
          let orderedTweets = res.data
            .sort(function (a, b) {
              return new Date(a.createdAt) - new Date(b.createdAt);
            })
            .slice()
            .reverse();
          this.$store.commit("updateTweets", orderedTweets);
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style lang="scss" scoped>
.idea {
  width: 40px;
  // position: absolute;
  
}
#ideaBox {
  position: fixed;
  bottom: 70px;
  right: 20px;
  display: grid;
  place-items: center;
  width: 50px;
  height: 50px;
  background: #d9dfcd;
  border: #b6c0af solid 2px;
  border-radius: 50%;
}
.goBack {
  margin-top: 10px;
  width: 30px;
  height: 30px;
  margin-left: -5px;
  background: #d9dfcd;
  border: #b6c0af solid 2px;
  img {
    width: 20px;
  }
}
section {
  border: #b6c0af solid 5px;
  margin: 20vh 10vw;
  width: 80vw;
  height: 300px;
  position: fixed;
  top: 0;
  left: 0;
  background: #97a58d;
  display: grid;
  place-items: center;
  form {
    display: grid;
  }
  input {
    padding: 5px;
    margin: 10px;
  }
}
#submitNewTweet {
  width: 60%;
  margin: 10px 20%;
  height: 30px;
  font-size: 12px;
  font-weight: 600;
  color: #829376;
  background: #d9dfcd;
  border-radius: 20px;

  display: grid;
  place-items: center;
}
@media screen and (min-width: 600px) {
#ideaBox {
right: 18vw;
}
section {
  border: #b6c0af solid 5px;
  margin: 25vh 20vw;
  width: 60vw;
}
}
@media screen and (min-width: 1100px) {
section {

  margin: 30vh 30vw;
  width: 40vw;
}
#ideaBox {
  
  bottom: 100px;
  right: 25vw;
  display: grid;
  place-items: center;
  width: 60px;
  height: 60px;
  background: #d9dfcd;
  border: #b6c0af solid 2px;
  border-radius: 50%;
  img {
    width: 50px;
  }

}
}
</style>