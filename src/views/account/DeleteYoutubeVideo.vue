<template>
  <div id="AddSong" class="container mx-auto max-w-4xl pt-20 px-6">

    <div class="text-gray-900 text-xl">Delete Youtube Video</div>
    <div class="bg-red-500 w-full h-1"></div>

    <div class="bg-white rounded px-8 pt-6 pb-8">
      <div v-for="(video,index) in videoStore.videos" :key="video" class="flex flex-wrap items-center">

        <div class="w-1/4 mr-auto mt-2 text-lg p-1 text-gray-900">
            {{++index}} {{video.title}}
          <iframe class="w-full h-20" :src="video.url"></iframe>
        </div>

        <div class="w-1/4 ml-auto p-1">
        <button
            class="
                float-right
                bg-transparent
                hover:bg-red-500
                text-gray-900
                font-semibold
                hover:text-white
                py-2
                px-4
                border
                border-red-500
                hover:transparent
            "
            @click="deleteVideo(video)"
        >
            Delete
        </button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import Swal from "sweetalert2";
import axios from "axios";
import {useVideoStore} from "../../store/video-store";
import {useUserStore} from "../../store/user-store";
import {ref} from "vue";

const videoStore = useVideoStore()
const userStore = useUserStore()


const errors = ref([])

const deleteVideo = async (video) => {

  Swal.fire({
    title: 'Are you sure you want to delete this?',
    text: 'You won\'t be able to revert this!',
    icon: 'warning',
    showCancelButton: true,
    confirmButtonText: 'Yes, Delete it',
    confirmButtonColor: '#3085d6',
    cancelButtonColor: '#d33',
  }).then(async (result) => {
    if (result.isConfirmed) {
      try {
        await axios.delete('api/youtube/' + video.id)

        videoStore.fetchVideosByUserId(userStore.id)

        Swal.fire(
            'Delete!',
            'Your video has been deleted.',
            'Success'
        )

      } catch (err) {
        errors.value = err.response.data.errors
      }
    }
  })
}
</script>

