<script setup>
import ProfileCard from './ProfileCard.vue'
import { ref } from 'vue'

const profileName = ref(null)
const profile = ref(null)

const name = ref('')
const link = ref(null)
const age = ref(null)
const bio = ref('')
const likes = ref([{ 'category': '', 'favorite': '' }])
const likeCount = ref(0)

const newProfile = ref({})


const nameExists = ref(null)

const fetchProfile = () => {
    fetch(`http://timoth.yt/api/rampup?name=${profileName.value}`, {
        method: 'GET',
        headers: {
            'Content-Type': 'application/json',
        },
    })
    .then(response => response.json())
    .then(data => profile.value = data)
    .then(() => addUI())
}

const addLike = () => {
    newProfile.value["LikeCount"]++
    console.log(newProfile.value["LikeCount"])
    newProfile.value['Likes'].push({
        'category': 'Category',
        'favorite': 'Your Favorite'
    })
}

const addUI = () => {
    if (profile.value.hasOwnProperty("message") && profile.value.message == "name not found in profiles" ) {
        nameExists.value = false
        return
    }
    nameExists.value = true

    newProfile.value = {
        "Name": profile.value["Name"],
        "Age": profile.value["Age"],
        "Bio": profile.value["Bio"],
    }
    if (link) {
        newProfile.value['Link'] = profile.value["Link"]
    }
    newProfile.value["LikeCount"] = Object.keys(profile.value.Likes).length
    let likesFormatted = [];
    for (var i = 0; i < newProfile.value["LikeCount"]; i++) {
        likesFormatted.push({"category": Object.keys(profile.value.Likes)[i], "favorite": Object.values(profile.value.Likes)[i]})
    }
    newProfile.value["Likes"] = likesFormatted;

    console.log(newProfile)
}

/* const editedProfile = () => {
    const newProfileObject = {
        'Name': newProfile['Name'],
        'Age': newProfile['Age'],
        'Bio': newProfile['Bio'],
        'Likes': importLikes(newProfile['Likes'])
    }
    if (newProfile['Link']) {
        body['Link'] = newProfile['Link']
    }

    fetch(`http://timoth.yt/api/rampup?name=${profileName.value}`, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
				body: JSON.stringify(newProfileObject)
})
    .then(response => response.json())
    .then(data => serverResponse.value = data.message)

    resetForm()
}

const importLikes = (likes) => {
    const likesObject = {}
    likes.value.forEach((like) => {
        likesObject[like.category] = like.favorite
    })
    return likesObject
} */

</script>

<template>
    <h1>Edit Profile</h1>
    <form id="fetchProfileForm" onsubmit="return false">
        <label for="profileName">Profile Name:</label>
        <input id="profileName" v-model="profileName" required>
        <input type="submit" @click="fetchProfile()">
    </form>

    <h1 v-if="nameExists != null && !nameExists">A profile with that name hasn't been made yet!</h1>

  <div class='form-wrapper' v-if="nameExists!=null && nameExists">

    <!-- This form tag wraps our inputs & allows us to require certain fields prior to submission -->
    <form id="newProfileForm" onsubmit="return false">
        <!-- These divs are for formatting to allow these inputs to sit side-by-side -->
        <div class='sub-form'>
            <div class='pair'>
                <!-- Input & Label for 'Name' attribute -->
                <label for="name">Name:</label>
                <input id="name" v-model="newProfile['Name']" placeholder="First Name" required>
            </div>
            <div class='pair'>
                <!-- Input & Label for 'Age' attribute -->
                <label for="age">Age:</label>
                <input id="age" v-model="newProfile['Age']" placeholder="Optional (put 0)" required>
            </div>
        </div>

        <!-- Optional Input & Label for 'Link' attribute -->
        <label for="link">Link:</label>
        <input id="link" v-model="newProfile['Link']" placeholder="Social Media URL (optional)">

        <!-- Input & Label for 'Bio' attribute -->
        <label for="bio">Bio:</label>
        <textarea id="bio" v-model="newProfile['Bio']" placeholder="A couple sentences about you..." required />

        <!-- Inputs & Labels for 'Likes' attribute -->
        <label for="likes">Likes:</label>
        <div id="likes" v-if="newProfile['LikeCount']" v-for="index in newProfile['LikeCount']" v-bind:key="index">
            <!-- This block dynamically generates a number of input pairs 
            for each Like according to the value of likeCount -->
            <input v-model="newProfile['Likes'][index - 1].category" placeholder="Category" required>
            <input v-model="newProfile['Likes'][index - 1].favorite" placeholder="Your Favorite" required>
            <!-- The removal button appears next to the last additional Like -->
            <button class="remove-like" v-if="index === likeCount && index > 1" @click="removeLike()">X</button>
        </div>
        <!-- Buttons for submission & adding more Like inputs -->
        <div class='sub-form'>
            <input class='form-button' type="submit" @click="editedProfile()">
            <button class='form-button' @click="addLike()">Add Like</button>
        </div>
    </form>

  </div>

  <h1>Preview </h1>
  <div v-if="nameExists">
    <ProfileCard :profile=newProfile />
  </div>

</template>

<style scoped>
.card {
  display: flex;
  flex-direction: column;
  width: 450px;
  height: 450px;
  overflow: hidden;
  overflow-y: auto;
  padding: 20px;
  margin: 10px;
  background:#404040;
}

#fetchProfileForm input {
    margin: 5px;
    background-color: #d1d1d1;
    border-radius: 5px;
}

.form-wrapper {
  width: 520px;
  display: flex;
  flex-direction: column;
}

.sub-form {
    width: 100%;
    display: flex;
    flex-direction: row;
}

.pair {
    display: flex;
    flex-direction: column;
}

.pair input {
    min-width: 250px;
}

.remove-like {
    width: 25px;
    height: 25px;
    background-color: rgb(255, 152, 152);
}

.form-button {
    width: 100px;
    height: 40px;
}

form {
    display: flex;
    flex-direction: column;
}

input {
    height: 30px;
}

textarea {
    height: 100px;
}

input, textarea, button {
    background-color: #d1d1d1;
    border-radius: 5px;
    margin: 10px 10px 10px 0;
}

</style>
