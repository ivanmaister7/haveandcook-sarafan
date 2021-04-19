<template>
  <v-container>
    <v-layout justify-space-around>
      <v-flex :xs6="!$vuetify.breakpoint.xsOnly">
        <div class="title mb-5"></div>
        <v-layout row justify-space-between>
          <v-flex class="px-1">
            <v-img :src="profile.userpic" ></v-img>
          </v-flex>
          <v-flex class="px-10">
            <v-layout column>
              <v-sheet
                  :max-width="model === 6 ? 128 : 256"
                  class="indigo lighten-4 text-center my-1"
                  elevation="10"
                  height="30"
                  width="100%"
                  rounded
              >
                <v-flex>{{profile.name}}</v-flex>
              </v-sheet>
              <v-sheet
                  :max-width="model === 6 ? 128 : 256"
                  class="indigo lighten-5 text-center my-1"
                  elevation="10"
                  height="30"
                  width="100%"
                  rounded
              >
                <v-flex>{{profile.lastVisit}}</v-flex>
              </v-sheet>
              <v-container class="text-left">
                <v-flex>
                  {{profile.subscriptions && profile.subscriptions.length}} subscriptions
                </v-flex>
                <router-link
                    v-if="isMyProfile"
                    :to="`/subscriptions/${profile.id}`"
                    class="text-decoration-none"
                >
                  {{profile.subscribers && profile.subscribers.length}} subscribers
                </router-link>
                <v-flex
                    v-else
                >
                  {{profile.subscribers && profile.subscribers.length}} subscribers
                </v-flex>
              </v-container>
            </v-layout>
          </v-flex>
        </v-layout>
        <v-btn
            class="my-8 text-center px-15"
            elevation="10"
            v-if="!isMyProfile"
            @click="changeSubscription"
        >
          {{isISubscribed ? 'Unsubscribe' : 'Subscribe'}}
        </v-btn>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import profileApi from 'api/profile'
export default {
  name: 'Profile',
  data() {
    return {
      profile: {}
    }
  },
  computed: {
    isMyProfile() {
      return !this.$route.params.id ||
          this.$route.params.id === this.$store.state.profile.id
    },
    isISubscribed() {
      return this.profile.subscribers &&
          this.profile.subscribers.find(subscription => {
            return subscription.subscriber === this.$store.state.profile.id
          })
    }
  },
  watch: {
    '$route'() {
      this.updateProfile()
    }
  },
  methods: {
    async changeSubscription() {
      const data = await profileApi.changeSubscription(this.profile.id)
      this.profile = await data.json()
    },
    async updateProfile() {
      const id = this.$route.params.id || this.$store.state.profile.id
      const data = await profileApi.get(id)
      this.profile = await data.json()
      this.$forceUpdate()
    }
  },
  beforeMount() {
    this.updateProfile()
  }
}
</script>

<style scoped>
img {
  max-width: 100%;
  height: auto;
}
</style>