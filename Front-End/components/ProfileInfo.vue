<template style="background-color: lightgoldenrodyellow;">
  <b-container class="bv-example-row mt-5">
    <b-row class="justify-content-md-center">
      <b-col col lg="2">
        <b-img rounded="circle" alt="Circle image" :src="this.urlPicProfil" style="width : 150px ; height : 150px">
        </b-img>
      </b-col>
      <b-col>
        <b-row>
          <b-col sm="8">
            <h2>{{ username }}</h2>
            <b-col v-if="isNotMyProfile">
            <b-row>
              <b-col lg="2">
                <h5>
                  <b-badge style="margin-left : 20% " href="#" variant="primary" v-on:click="follow">follow</b-badge>
                </h5>
              </b-col>
              <b-col lg="2">
                <h5>
                  <b-badge style=" margin-right : 100%" href="#" variant="danger" v-on:click="unFollow">unfollow
                  </b-badge>
                </h5>
              </b-col>
            </b-row>
          </b-col>
          </b-col>
          <b-col sm="4" v-if="!isNotMyProfile">
            <font-awesome-icon icon="user-edit" v-on:click="redirectEditProfil" style="width :50% ; height : 50%" />

          </b-col>
        </b-row>
        <p></p>
      </b-col>
    </b-row>
    <b-row class="mb-3">
      <b-col lg="2"></b-col>
      <b-col>
        <b-row>
          <b-col>
            <Label style="font-weight:bold; display:inline; margin-right:120px"><strong> {{nbrpost}}</strong>
              Posts</Label>
          </b-col>
          <b-col>
            <Label v-b-modal.modal-2 style="font-weight:bold; display:inline; margin-right:120px">Follower</Label>
            <div class="container">
              <b-modal id="modal-2" title="Follower">
                <Follower type="followers" :userId="iduser? iduser: user._id"></Follower>
              </b-modal>
            </div>
          </b-col>
          <b-col>
            <Label v-b-modal.modal-3 style="font-weight:bold; display:inline; margin-right:120px">Following</Label>
            <div class="container">
              <b-modal id="modal-3" title="Following">
                <Follower type="followings" :userId="iduser? iduser: user._id"></Follower>
              </b-modal>
            </div>
          </b-col>
        </b-row>
      </b-col>
    </b-row>
    <hr class="my-5" />
  </b-container>
</template>

<script>
  import EditProfil from '~/components/EditProfil.vue'
  import Follower from '~/components/Follower.vue'

  export default {
    components: {
      EditProfil,
      Follower
    },
    props: ['user', 'iduser', "nbrpost"],
    data() {
      return {
        username: "",
        urlPicProfil: "",
        bio: "",
        followdsss: false
      }
    },
    methods: {
      follow(e) {
        this.$axios.post("/followers", {
          following: this.iduser
        })
      },
      unFollow(e) {
        this.$axios.delete("/followers/" + this.iduser).then(_ => {
        })
      },
      redirectEditProfil(e) {
        window.location.href = "/editProfil"
      }
    },
    created: async function () {
      if (this.user) {
        this.username = `${this.user.firstName} ${this.user.lastName}`;
        this.urlPicProfil = this.$axios.defaults.baseURL + `${this.user.profileImage}`;
        this.bio = this.user.bio;

      } else {
        let userProfile = await this.$axios.get(`/users/${this.iduser}`)
          .catch(e => this.$router.push("/"))
        this.username = `${userProfile.data.data.firstName} ${userProfile.data.data.lastName}`;
        this.urlPicProfil = this.$axios.defaults.baseURL + `${userProfile.data.data.profileImage}`;
        this.bio = userProfile.data.data.bio;
      }

    },
    computed: {
      isFollowed: function () {
        // TODO check if is followed by the user or not

        return true;
      },
      isNotMyProfile: function () {
        return this.user ? this.user._id !== this.$auth.user._id : this.iduser !== this.$auth.user._id;
      }
    },
    mounted: function () {
      let isFoll = false;
      let follows = this.$axios.get(`/users/${this.iduser}/followings`).then(e => {
        e.data.data.forEach(element => {
          if (element.follower._id === this.$auth.user._id) {
            isFoll = true
          }
        });
        this.followdsss = isFoll;
      })

    }
  }

</script>
<style scoped>

</style>
