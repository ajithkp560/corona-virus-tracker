<template>
<f7-app :params="f7params" >


  <!-- Your main view, should have "view-main" class -->
  <f7-view main class="safe-areas" url="/"></f7-view>


</f7-app>
</template>
<script>

  import routes from '../js/routes.js';

  export default {
    data() {
      return {
        // Framework7 Parameters
        f7params: {
          name: 'corona-virus-tracker', // App name
          theme: 'auto', // Automatic theme detection


          // App routes
          routes: routes,
        },
        password: '',
      }
    },
    methods: {
    },
    mounted() {
      this.$f7ready((f7) => {

        // Call F7 APIs here
      });
    }
  }
</script>