<template>
  <div>
    <div @click="viewComments" class="comments">
      <img
        src="../assets/comments.svg"
        alt="comments"
      />
      <p class="numberofcomments">{{ tweetComments.length }}</p>
    </div>

    <section v-if="isCommentsOpen === true" class="commentSection"> 
      <form class="newComment" action="javascript:void(0)" autocomplete="off">
        <input 
          type="text"
          placeholder="Make a comment"
          autocomplete="null"
          :id="commentTweetId"
          class="commentInput"
        >
     
        <img src="../assets/send.png"  alt="send comment" class="sendComment">
        <input type="submit" value="send" @click="postComment" class="submitAComment" />
      </form>
      <div class="aComment" v-for="object in tweetComments" :key="object.id">
        <like-comment :commentId="object.commentId">
          </like-comment><friend-profile
          :otherUserId="object.userId"
          :otherUserName="object.username"
          :getTweetsFunction="getComments"
          :getUserFollowsFunction="getUserFollows"
        ></friend-profile>
        <div class="line"></div>
        <p class="commentContent">{{ object.content }}</p>
        <edit-comment v-if="storeCurrentUser.userId === object.userId" :editCommentId="object.commentId" :getTheComments="getComments"></edit-comment>
        
      </div>
      
    </section>
  </div>
</template>

<script>
import axios from "axios";
import FriendProfile from "./FriendProfile.vue";
import EditComment from "./EditComment.vue";
import LikeComment from './LikeComment.vue';
export default {
  components: { EditComment, LikeComment, FriendProfile },
  name: "comment-section",
  data() {
    return {
      isCommentsOpen: false,
      tweetComments: [],
      showMakeComment: false,
      userFollows: [],
    };
  },
  computed: {
    storeCurrentUser() {
      return this.$store.state.currentUser;
    },
  },
  mounted() {
    this.getComments();
  },
  props: {
    commentTweetId: Number,
  },
  methods: {
    
    viewComments: function () {

      if (this.isCommentsOpen === false) {
        this.isCommentsOpen = !this.isCommentsOpen;
      } else {
        this.isCommentsOpen = !this.isCommentsOpen;
      }
    },
    postComment: function () {
      axios
        .request({
          method: "POST",
          url: `${process.env.VUE_APP_API_KEY}/api/comments`,
          headers: {
            "Content-Type": "application/json"
            // "X-Api-Key": `${process.env.VUE_APP_API_KEY}`,
          },
          data: {
            content: document.getElementById(this.commentTweetId).value,
            loginToken: this.storeCurrentUser.loginToken,
            tweetId: this.commentTweetId,
          },
        })
        .then((res) => {
          console.log(res.data + "comment succesfully posted");
          document.getElementById(this.commentTweetId).value = null;
          this.getComments()
        })
        .catch((err) => {
          console.log(err);
        });
        this.getComments();
    },

    getComments: function () {
      axios
        .request({
          method: "GET",
          url: `${process.env.VUE_APP_API_KEY}/api/comments`,
          headers: {
            "Content-Type": "application/json",
            // "X-Api-Key": `${process.env.VUE_APP_API_KEY}`,
          },
          params: {
            tweetId: this.commentTweetId,
          },
        })
        .then((res) => {
          this.tweetComments = res.data;
          this.showMakeComment = false;
        })
        .catch((err) => {
          console.log(err);
        });
    },
      getUserFollows() {
      axios
        .request({
          method: "GET",
          url: `${process.env.VUE_APP_API_KEY}/api/follows`,
          headers: {
            "Content-Type": "application/json",
            // "X-Api-Key": `${process.env.VUE_APP_API_KEY}`,
          },
          params: {
            userId: this.storeCurrentUser.userId,
          },
        })
        .then((res) => {
         this.userFollows = res.data;
        this.userFollows.push(this.storeCurrentUser);
         console.log(this.userFollows);
        })
        .catch((err) => {
          console.log(err);
        });
      },
 },
};
</script>

<style lang="scss" scoped>
.comments {
  margin-left: 200px;
  margin-bottom: -15px;
  padding-bottom: 10px;
   img {
    width: 20px;
    z-index: 5;
  }
  .numberofcomments {
    margin-top: -23px;
    font-size: 10px;
  }
}

.commentSection {
  height: 100%;

padding: 20px 15px;
  // text-align: left;
  // padding: 15px;
  margin-top: 10px;
}
.commenterUsername {
  font-weight: 550;
  margin-left: -120px;
  
}
.commentContent {
  font-size: 14px;
  text-align: left;
  padding: 0px 0px 15px 30px;
  width: 70%;
}

.aComment {
  padding: 15px 0;
  box-shadow: #88997c 0px 0px 2px;
}
.sendComment {
  width: 30px;
  margin-right: -30px;

  margin-bottom: -10px;

}
form {
  margin-bottom: 20px;
}
.commentInput {
 width: 60%;
 padding: 5px 15px;
 word-break: break-word;
 margin-right: 10px;
}
.submitAComment {
  opacity: 0;
  height: 35px;
}


@media screen and (min-width: 600px) {
 .comments {
  margin-left: 320px;

  padding-bottom: 20px;
  img {
    width: 20px;
    z-index: 5;
  }
  .numberofcomments {
    margin-top: -23px;
    font-size: 10px;
  }
}
}
@media screen and (min-width: 1100px) {
 
}
</style>