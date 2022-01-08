<template>
  <v-app>
    <v-app-bar
      app
      flat
    >
        <v-tabs
          color="primary"
        >
          <v-tabs-slider></v-tabs-slider>

          <v-tab href="#tab-1">
            Home
          </v-tab>

          <v-tab href="#tab-2">
            Comments
          </v-tab>
          <v-tab-item value="tab-1">
            <div class="d-flex align-center">
              <Profile/>
            </div>
          </v-tab-item>
          <v-tab-item value="tab-2">
            <TableComments/>
          </v-tab-item>
        </v-tabs>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <!-- <HelloWorld/> -->
      <!-- <Profile/> -->
    </v-main>
  </v-app>
</template>

<script>
// import HelloWorld from './components/HelloWorld';
import Profile from './components/Profile';
import TableComments from './components/TableComments';

export default {
  name: 'App',

  components: {
    // HelloWorld,
    Profile,
    TableComments,
  },

  data: () => ({
    //
  }),
};
</script>

<style>
 @import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css');
</style>