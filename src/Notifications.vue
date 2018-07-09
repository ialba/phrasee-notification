<template>
  <v-menu
  offset-y
  transition="slide-y-transition"
  max-width="500">
    <v-btn
          slot="activator"
          dark
          icon
          @click="unreadNotifications = 0"
        >
        <v-badge color="red">
          <span slot="badge">{{ unreadNotifications }}</span>
          <v-icon color="grey">
            notification_important
          </v-icon>
        </v-badge>
    </v-btn>
    <v-list two-line>
      <v-subheader>Notifications </v-subheader>
      <v-list-tile
        v-for="(notification, index) in notifications"
        :key="index"
        @click=""
      >
        <v-list-tile-avatar>
          <img :src="'https://randomuser.me/api/portraits/men/'+getRandomInt(50)+'.jpg'">
        </v-list-tile-avatar>

        <v-list-tile-content class="body-1">
          <v-list-tile-title v-if="notification.type == 'Like'" > <span class="blue--text">{{ notification.likes | printLikesComments }}</span> liked your post</v-list-tile-title>
          <v-list-tile-title v-else> <span class="blue--text">{{ notification.comments | printLikesComments }}</span> commented your post</v-list-tile-title>
          <v-list-tile-sub-title>{{ notification.post.title | maxLength(50) }}</v-list-tile-sub-title>
        </v-list-tile-content>
      </v-list-tile>
    </v-list>
  </v-menu>
</template>

<script>
  import axios from 'axios'
  export default {
    data () {
      return {
        unreadNotifications : 0,
        notifications: []
      }
    },
    mounted(){
      // Normally i would extern this call in a helper ApiHelper.js but as a proof of concept this is enough
      axios.get('http://www.mocky.io/v2/5b4315f12e00004c002230c3')
      .then(response => {
        this.notifications = response.data
        this.unreadNotifications = response.data.length
      })
    },
    methods : {
      // We need this for the mocking of the user's avatars
      getRandomInt(max) {
        return Math.floor(Math.random() * Math.floor(max));
      }
    },
    // I will normally add this to the vue instance with vue.use so its usable all arround the application
    filters : {
      maxLength : (data, length) => {
        if(data == '' ) return ''
        let substrData =  data.substring(0, length)
        if(data.length > length) substrData = substrData + '...'
        return substrData
      },
      printLikesComments : data => {
        if(data[0].name == '') return 'User'
        let dataPersons = data[0].name
        if(data[1] != null ) dataPersons = dataPersons.concat(', '+data[1].name)
        if(data[2] != null ) dataPersons = dataPersons.concat(' and '+(data.length-2)+' others')
        return dataPersons
      }
    }
  }
</script>
