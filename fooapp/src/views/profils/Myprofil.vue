<template>
  <div>
      <vs-navbar
        :type="type"
        v-model="activeItem"
        class="nabarx spacing-added-to-nav">
        <div slot="title">
          <vs-navbar-title>
            Logo under construction
          </vs-navbar-title>
        </div>

        <vs-navbar-item class="spacing-navbar-element">
          <span @click="myprofil">{{ name }}</span>
        </vs-navbar-item>
          <vs-navbar-item class="spacing-navbar-element">
          <span  @click="goMAinFeed">Main Feed</span>
         </vs-navbar-item>
        <vs-navbar-item v-if="userStatus ==='admin'"  class="spacing-navbar-element">
          <span @click="adminDashboard">Admin dashboard</span>
        </vs-navbar-item>
        <vs-navbar-item class="spacing-navbar-element">
          <span @click="logout">Logout</span>
        </vs-navbar-item>
      </vs-navbar>
      <div class="profil-main-info">
      <vs-avatar class="avaatar" size="150px" :src= profileimage />
       <h2 class="profil-username"> Welcome {{ name }} </h2>
        <div class="profil-interact-info">
          <vs-row vs-justify="center">
            <vs-col type="flex" vs-justify="center" vs-align="center" vs-w="12" vs-sm="12">
            <vs-card>
            <div slot="header">
            <h4>
              Followers : {{myfollowers}}
            </h4>
            </div>
            </vs-card>
            </vs-col>
            </vs-row>
            <vs-row vs-justify="center">
            <vs-col type="flex" vs-justify="center" vs-align="center" vs-w="12" vs-sm="12">
            <vs-card>
            <div slot="header">
            <h4>
              Likes : {{mylikes}}
            </h4>
            </div>
            </vs-card>
            </vs-col>
            </vs-row>
            </div>
          <vs-row vs-justify="center">
          <vs-col type="flex" vs-justify="center" vs-align="center" vs-w="5" vs-sm="12">
          <vs-card>
          <div slot="header">
          <h4>
            My posts
          </h4>
          </div>
          </vs-card>
          </vs-col>
          </vs-row>
          <Profilfeed :userId="userId"/>
     </div>
     <Clicktopost />
   </div>   
</template>
<script>
import axios from 'axios';
import Profilfeed from './Profilfeed.vue';
import Clicktopost from '../posts/Clicktopost'
const Cookies = require("js-cookie");
export default {
   name:'Myprofil',
   components:{
       Profilfeed,
       Clicktopost
    },
    data: () => ({
      profileimage: "",
      name: Cookies.get("name"),
      userId: Cookies.get("_id"),
      userStatus: Cookies.get("status"),
      myfollowers: 0,
      popupActivo: false,
      type: "type",
      activeItem: "activeItem",
      mylikes:null,
    }),
    async mounted(){
      const mylikes= await axios.get(`/api/likes/mylike/${this.userId}`)
      const followers= await axios.get(`/api/loginsignup/${this.userId}`);
      if(mylikes && followers){
        // console.log(followers.data)
          this.mylikes= mylikes.data.length;
          this.myfollowers= followers.data.followingYou
      }
      let string = Cookies.get('img');
      let target = string.search('upload/');
      this.profileimage = string.slice(0,target+7) + 'w_1000,c_fill,ar_1:1,g_auto,r_max/' + string.slice(target+7);
},

  methods: {
    logout() {
      Cookies.remove("name");
      Cookies.remove("_id");
      Cookies.remove("status");
      Cookies.remove("img");
      document.location.replace('/');
    },
    adminDashboard(){
      this.$router.push("/admindashboard");
    },
    goToPost(id){
      this.$router.push(`/post/${id}`)
    },
    goMAinFeed(){
      window.location.replace('/mainfeed');
    },
    myprofil(){
    this.$router.push('/myprofil')
    }
  },
}

</script>
<style scoped>
.spacing-added-to-nav {
  padding: 1rem 2.4rem;
  background: #fff;
}
.spacing-navbar-element {
  margin: 0 0.6rem;
}
.button-nav-spacing {
  width: 7.6rem;
  border-radius: 25px;
}
span {
  cursor: pointer;
}
.avaatar {
 margin: 4rem auto;

}
.profil-interact-info{
    width: 58rem;
    margin: 4rem auto;
    display: flex;

}
.profil-main-info{
 display: flex;
 flex-direction: column;
 justify-content: center;
 align-items: center;   
}
.profil-username{
    text-align: center;
}

</style>