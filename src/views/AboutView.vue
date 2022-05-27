<template>
  <div class="about">
    <br />
    <v-form
      ref="form"
      v-model="valid"
      lazy-validation
      @submit="validate"
      onSubmit="return false;"
    >
      <v-text-field v-model="id" label="通行碼" required></v-text-field>
      <v-btn color="success" class="mr-4" @click="validate">登入</v-btn>
    </v-form>
  </div>
</template>

<script>
export default {
  components: {},
  data: () => ({
    id: "",
  }),
  methods: {
    validate() {
      alert("Maybe u r ADMIN?");
      this.$cookie.set("access_code", this.id, 30);
    },
  },
  created: function () {},
};
</script>