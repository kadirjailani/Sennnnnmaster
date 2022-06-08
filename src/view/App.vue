<template>
  <div class="wrapper">
    <!-- Left side: form -->
    <div class="form inner-wrapper">
      <div class="block">
        <h1>Hello there!</h1>
        <h4>
          Eaque possimus reprehenderit a ut dolorem. Quam aut aut eveniet et voluptate accusantium
          commodi placeat. Incidunt ad error laboriosam vitae sed vel maiores.
        </h4>

        <div class="divider"></div>

        <div v-show="showError" class="errors">
          <h2 v-for="error in errors" v-bind:key="error.index">{{ error }}</h2>
        </div>
        <div class="form-group">
          <label for="name">Name</label>
          <div class="form-in-a-row">
            <input
              type="text"
              id="firstName"
              class="form-control left"
              placeholder="First name"
              v-model="newUser.first_name"
            />
            <input
              type="text"
              id="lastname"
              class="form-control right"
              placeholder="Last name"
              v-model="newUser.last_name"
            />
          </div>
        </div>

        <div class="form-group">
          <label for="email">Your Email Address</label>
          <input
            type="email"
            id="email"
            class="form-control"
            placeholder="Email address"
            v-model="newUser.email"
          />
        </div>

        <button v-on:click="checkForm()" v-bind:disabled="showAddButton" class="btn">
          add new profile
        </button>
      </div>
    </div>

    <!-- Right side: User List -->
    <div class="list">
      <!-- title -->
      <h1>User list</h1>

      <div v-if="!user.data.length > 0" class="nothing-to-Show">
        <img src="../images/example.png" />
        <h4>Add more user from left panel.</h4>
      </div>
      <div v-else>
        <!-- User template loop -->
        <div v-for="item in user.data" v-bind:key="item.index" class="user-row">
          <div class="details">
            <div class="image-wrapper">
              <img v-bind:src="item.avatar" v-bind:alt="item.first_name" />
            </div>
            <div class="personal-details">
              <h3>{{ item.first_name }} {{ item.last_name }}</h3>
              <p>{{ item.email }}</p>
            </div>
          </div>
          <div class="action">
            <button v-on:click="deleteThis(item)">
              <img src="../images/close.svg" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'
export default {
  data() {
    return {
      user: {
        data: [],
      },
      newUser: {
        email: null,
        first_name: null,
        last_name: null,
        avatar: null,
      },
      showAddButton: false,
      showError: false,
      errors: [],
    }
  },
  components: {},
  watch: {
    // showAddButtonCheck: function () {
    //   let showIt = false
    //   if (this.newUser.first_name !== '' || this.newUser.first_name.length > 2) {
    //     showIt = true
    //   }
    //   this.showAddButton = showIt
    // },
  },
  methods: {
    checkForm: function () {
      this.errors = []

      if (!this.newUser.first_name) {
        this.errors.push('First name required.')
      }

      if (!this.newUser.last_name) {
        this.errors.push('Last name required.')
      }

      if (!this.newUser.email) {
        this.errors.push('Email required.')
      } else if (!this.validEmail(this.newUser.email)) {
        this.errors.push('Valid email required.')
      }

      if (!this.errors.length) {
        this.addNewProfile();
      } else {
        this.showError = true;
      }
    },
    validEmail: function (email) {
      var re =
        /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
      return re.test(email)
    },
    async addNewProfile() {
      let randomProfile = Math.floor(Math.random() * (12 - 1) + 1);
      let response = await fetch(`https://reqres.in/api/users/${randomProfile}`).catch((error) => {
        console.log(error)
      })
      let responseData = await response.json()

      this.newUser.avatar = responseData.data.avatar;

      this.user.data.unshift({
        first_name: this.newUser.first_name,
        last_name: this.newUser.last_name,
        email: this.newUser.email,
        avatar: this.newUser.avatar,
      })

      this.showError = false;
      this.clearCurrentData()
    },
    clearCurrentData: function () {
      this.newUser.first_name = ''
      this.newUser.last_name = ''
      this.newUser.email = ''
    },
    deleteThis: function (item) {
      let data = this.user.data
      let getIndex = this.user.data.indexOf(item)
      data.splice(getIndex, 1)
    },
  },
  async mounted() {
    const response = await fetch('https://reqres.in/api/users')
    let responseData = await response.json()
    this.user = responseData
  },
  //   async created() {
  //     const response = await fetch('https://reqres.in/api/users')
  //     let responseData = await response.json()
  //     this.user = responseData;
  //   },
}
</script>

<style scoped lang="scss">
h1 {
  color: white;
  font-size: 60px;
  font-family: Arial, Helvetica, sans-serif;
}
</style>