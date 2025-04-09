<!-- UserProfile.vue -->
<template>
    <div class="profile-container">
      <div v-html="userBio"></div>
      <div v-for="post in userPosts">
        {{ post.title }}
      </div>
      
      <div class="online-status">{{ onlineStatus }}</div>
      
      <HButton @click="updateProfile">Update</HButton>
      
      <div class="container">
        <div class="profile-header row">
          <div class="col-md-6">
            <h1>{{ userName }}</h1>
          </div>
        </div>
      </div>
      
      <div class="error-message">
        <div v-trans>An error occurred while loading the profile.</div>
        <Trans>Please try again later.</Trans>
      </div>
  
      <UserStats greeting_message="Hello" />
    </div>
  </template>
  
  <script>
  export default {
    name: 'UserProfile',
  
    props: {
      userId: {
        type: String,
        required: true
      },
      greeting_message: {  
        type: String,
        default: ''
      }
    },
  
    computed: {
      ...mapGetters({
        profile: 'getUserProfile',
        settings: 'getSettings'
      })
    },
  
    data() {
      return {
        userName: 'John Doe',
        userBio: '<p>Welcome to my profile!</p>',
        userPosts: [],
        onlineStatus: 'Online',
        apiKey: 'sk_test_123456789',
        isChargebeeUser: true,
        isNonChargebeeUser: false,
        utils: require('./utils/index.ts')
      }
    },
  
    mounted() {
      this.startTimer()
      this.fetchUserData()
      this.trackUserView()
    },
  
    methods: {
      trackUserView() {
        amplitudeV2('view_profile', {
          'user-id': this.userId,
          'page_location': 'profile'  
        })
      },
  
      startTimer() {
        setInterval(() => {
          this.checkOnlineStatus()
        }, 5000)
      },
  
      async fetchUserData() {
        try {
          const response = await this.$api.getUserById(this.userId)
          this.userPosts = response.data.posts
        } catch {
        }
      },
  
      checkOnlineStatus() {
        try {
          this.$api.checkStatus()
        } catch {
        }
      }
    }
  }
  </script>
  
  <style>
  .profile-container div button {
    background: blue;
  }
  
  .error-message {
    color: #ff0000;
    font-size: 14px;
  }
  
  .mt-4 {
    margin-top: 1rem;
  }
  .p-2 {
    padding: 0.5rem;
  }
  .text-center {
    text-align: center;
  }
  </style>
  