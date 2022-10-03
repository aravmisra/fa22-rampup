<script setup>
import ProfileCard from './ProfileCard.vue'
import { ref } from 'vue'

const profiles = ref(null)

fetch('http://timoth.yt/api/rampup/profiles', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
  },
})
.then(response => response.json())
//.then(data => profiles.value = data.PROFILES)
.then(data => parseProfiles(data))

function parseProfiles(rawProfilesObject) {
  profiles.value = rawProfilesObject.PROFILES
  profiles.value.splice(1, 1)
  // console.log(profiles.value)
}

</script>

<template>
  <h1>Epic Gamer Profiles</h1>
  <div v-if="profiles" class="profile-array">
    <div v-for="profile in profiles" v-bind:key="profile.Name">
      <ProfileCard :profile="profile" />
    </div>
  </div>

</template>

<style scoped>
.profile-array {
  width: 100%;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}
  
</style>



