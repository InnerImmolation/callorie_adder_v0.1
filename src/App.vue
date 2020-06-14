<template>
  <v-app>
    <v-content>
      <Adder/>
    </v-content>
  </v-app>
</template>

<script>
import Adder from './components/Adder';

export default {
  name: 'App',

  components: {
    Adder,
  },

  data: () => ({
    //
  }),
};
</script>
