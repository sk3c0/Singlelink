<template>
  <section class="flex flex-col p-8 items-center flex-grow bg-gray-100 overflow-scroll">
    <h1 class="text-gray-800 font-semibold text-2xl w-full mb-4">Settings</h1>
    <div class="flex flex-col p-6 bg-white shadow rounded w-full mb-8">
      <h2 class="text-gray-800 font-semibold text-lg w-full mb-2">Profile details</h2>
      <form class="flex flex-col">
        <div class="flex flex-row mb-3">
          <div class="flex flex-col w-1/2 mr-4">
            <label class="font-medium text-sm text-gray-800" for="name">Full Name</label>
            <input class="p-2 mt-2 text-sm border-solid border-gray-300 rounded border" id="name" type="text" placeholder="e.g. Jane Doe" v-model="user.name"/>
          </div>
          <div class="flex flex-col w-1/2">
            <label class="font-medium text-sm text-gray-800" for="email">Email Address</label>
            <input class="p-2 mt-2 text-sm border-solid border-gray-300 rounded border" id="email" type="email" placeholder="e.g. jane@gmail.com" v-model="user.email"/>
          </div>
        </div>
        <div class="flex flex-row mb-6">
          <div class="flex flex-col w-1/2 mr-3">
            <label class="font-medium text-sm text-gray-800" for="handle">Handle</label>
            <div class="flex flex-row rounded border border-solid border-gray-300 text-sm mt-2 overflow-hidden">
              <span class="flex p-2 bg-gray-100 border text-gray-700 border-solid border-gray-300 border-t-0 border-l-0 border-b-0">singlelink.co/u/</span>
              <input class="p-2 flex-grow" id="handle" type="text" placeholder="e.g. janedoe" v-model="user.active_profile.handle"/>
            </div>
          </div>
          <div class="flex flex-col w-1/2">
            <label class="font-medium text-sm text-gray-800" for="visibility">Visibility</label>
            <select class="p-2 mt-2 text-sm border-solid border-gray-300 rounded border" id="visibility" v-model="this.user.active_profile.visibility">
              <option value="unpublished">Unpublished, not viewable</option>
              <option value="published">Public, no sensitive content (Most used)</option>
              <option value="published-18+">Public, sensitive content warning</option>
            </select>
          </div>
        </div>
        <button type="button" class="inline-flex p-3 text-sm text-white text-center bg-indigo-600 hover:bg-indigo-700 rounded font-semibold w-auto max-w-xs justify-center align-center">Save changes</button>
      </form>
    </div>
    <div class="flex flex-row p-6 bg-white shadow rounded justify-center items-center w-full mb-8">
      <div class="flex flex-col mr-auto w-1/2">
        <h2 class="text-gray-800 font-semibold text-lg w-full">Reset your password</h2>
        <p class="text-gray-600 font-medium">Forgot your password? Click the button to the right to have a reset link sent to your account email.</p>
      </div>
      <button type="button" @click="open_info_modal" class="ml-2 flex p-3 text-sm text-white text-center bg-blue-600 hover:bg-blue-700 rounded font-semibold w-1/3 justify-center align-center">Request reset link</button>
    </div>
    <div class="flex flex-row p-6 bg-white shadow rounded justify-center items-center w-full mb-8">
      <div class="flex flex-col mr-auto w-1/2">
        <h2 class="text-gray-800 font-semibold text-lg w-full">Delete your account</h2>
        <p class="text-gray-600 font-medium">Done with Singlelink? Click the button on your right to delete your account and all related info.</p>
      </div>
      <button type="button" @click="open_modal" class="ml-2 flex p-3 text-sm text-white text-center bg-red-600 hover:bg-red-700 rounded font-semibold w-1/3 justify-center align-center">Delete your account</button>
    </div>
    <div v-if="modal" @click="close_modal" class="w-screen h-screen absolute top-0 left-0 right-0 bottom-0 z-50 flex items-center justify-center" style="background: rgba(0,0,0,.5); backdrop-filter: saturate(180%) blur(5px);">
      <div v-on:click.stop class="flex flex-col p-6 bg-white shadow rounded w-full max-w-lg">
        <h2 class="text-gray-800 font-semibold text-xl">Are you sure?</h2>
        <p class="text-gray-600 text-sm">Deleting your account is irreversible, please confirm to continue.</p>
        <button @click="attempt_delete" type="button" class="mt-4 w-full p-4 text-center text-md text-white bg-red-600 hover:bg-red-700 rounded font-semibold">Yes, delete my account</button>
      </div>
    </div>
    <div v-if="info_modal" @click="close_info_modal" class="w-screen h-screen absolute top-0 left-0 right-0 bottom-0 z-50 flex items-center justify-center" style="background: rgba(0,0,0,.5); backdrop-filter: saturate(180%) blur(5px);">
      <div v-on:click.stop class="flex flex-col p-6 bg-white shadow rounded w-full max-w-lg">
        <h2 class="text-gray-800 font-semibold text-xl">Password reset requested</h2>
        <p class="text-gray-600 text-sm">A password reset link has been sent to your account email inbox successfully. Make sure to check your spam folder.</p>
        <button @click="close_info_modal" type="button" class="mt-4 p-3 text-center text-md text-white bg-indigo-600 hover:bg-indigo-700 rounded font-semibold">Close</button>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  layout: 'dashboard',
  middleware: 'authenticated',
  data: function () {
    return {
      info_modal: false,
      modal: false,
      user: {
        name: '',
        email: '',
        active_profile: {
          handle: '',
          visibility: ''
        }
      }
    };
  },
  mounted: function() {
    this.fetch_user_data();
  },
  methods: {
    open_info_modal: function() {
      return this.info_modal = true;
    },
    close_info_modal: function() {
      return this.info_modal = false;
    },
    open_modal: function() {
      return this.modal = true;
    },
    close_modal: function() {
      return this.modal = false;
    },
    attempt_delete: function() {
      //this.close_modal();
      this.$nuxt.$loading.start();
      this.$router.push('/');
      this.$nuxt.$loading.finish();
      return;
    },
    fetch_user_data: function() {
      this.$axios.$post('/user/fetch', {
        token : this.$store.getters['auth/get_token']
      })
        .then((response) => {
          this.user = response;
        })
        .catch((error) => {
          console.log('Error fetching user data');
          console.log(error);
        });
    },
  }
};
</script>
