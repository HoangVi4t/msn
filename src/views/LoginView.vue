<template>
  <div id="register">
    <TopNavigation/>
    <div class="w-full p-6 flex justify-center items-center">
      <div class="w-full max-w-xs">
        <div class="bg-black p-8 shadow rounded mb-6">
          <h1 class="mb-6 text-lg text-gray-100 font-thin">Let's get rocking!</h1>

          <div class="mb-4">
            <TextInput label="Email" :labelColor="false" placeholder="Enter your email" v-model:input="email"
                       inputType="text" :error="errors.email ? errors.email[0] : ''"
            />
          </div>

          <div class="mb-4">
            <TextInput label="Password" :labelColor="false" placeholder="Enter your password123?" v-model:input="password"
                       inputType="password" :error="errors.email ? errors.email[0] : ''"
            />
          </div>


          <button class="
              block
              w-full
              bg-green-500
              text-white
              rounded-sm
              py-3
              text-sm
              tracking-wide
              "
                  type="submit"
                  @click="login"
          >
            Login
          </button>
        </div>

        <p class="text-center text-md text-gray-900">
          Don't have an account yet?
          <router-link to="register" class="text-blue-500 no-underline hover:underline">
            Register
          </router-link>
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import {ref} from "vue";
import axios from "axios";
import TextInput from "../components/global/TextInput.vue";
import {useUserStore} from "../store/user-store";
import {useProfileStore} from "../store/profile-store";
import {useSongStore} from "../store/song-store";
import {usePostStore} from "../store/post-store";
import {useVideoStore} from "../store/video-store";
import {useRouter} from "vue-router";
import TopNavigation from "@/components/structure/TopNavigation.vue";

const userStore = useUserStore()
const profileStore = useProfileStore()
const songStore = useSongStore()
const postStore = usePostStore()
const videoStore = useVideoStore()

const router = useRouter()

let errors = ref([])
let email = ref(null)
let password = ref(null)

const login = async () => {

  errors.value = []
  try {

    let res = await axios.post('api/login', {
      email: email.value,
      password: password.value,
    })
    axios.defaults.headers.common['Authorization'] = 'Bearer ' + res.data.token

    userStore.setUserDetails(res)
    await profileStore.fetchProfileById(userStore.id)
    await songStore.fetchSongsByUserId(userStore.id)
    await postStore.fetchPostsByUserId(userStore.id)
    await videoStore.fetchVideosByUserId(userStore.id)

    router.push('/account/profile/' + userStore.id)
  } catch (err) {
    errors.value = err.response.data.errors
  }
}

</script>

