<template>
  <div>
    <v-menu offset-y>
      <template v-slot:activator="{ on, attrs }">
        <v-icon
          color="black"
          class="primary pa-1 border-1"
          v-bind="attrs"
          v-on="on"
          >mdi-menu</v-icon
        >
      </template>
      <v-list>
        <v-list-item v-for="(item, index) in genres" :key="index">
          <v-list-item-title
            @click="$router.replace(`/genre/${item.id}`)"
            class="pointer"
          >
            {{ formattedItems[index] }}
          </v-list-item-title>
        </v-list-item>
      </v-list>
    </v-menu>
  </div>
</template>

<script>
import { GET_GENRES } from "@/graphql/queries";
export default {
  name: "GenreList",
  data: () => ({
    genres: null,
  }),
  computed: {
    formattedItems() {
      return this.genres.map((item) => this.formatString(item.name));
    },
  },
  methods: {
    formatString(str) {
      return str.toLowerCase().replace(/(^|\s)[a-z]/g, function (a) {
        return a.toUpperCase();
      });
    },
    getMovies() {
      this.$apollo
        .query({
          query: GET_GENRES,
          variables: {
            options: {
              sort: [
                {
                  name,
                },
              ],
            },
          },
        })
        .then(({ data }) => {
          this.loading = false;
          this.genres = data.genres.sort((a, b) => {
            if (a.name < b.name) {
              return -1;
            }
            if (a.name > b.name) {
              return 1;
            }
            return 0;
          });
        });
    },
  },
  created() {
    this.getMovies();
  },

  watch: {
    $route: function () {
      this.getMovies();
    },
  },
};
</script>

<style></style>
