<template>
  <div class="article">
    <ul>
      <li v-for="article in list" :key="article.slug">
        <nuxt-link :to="article.slug">
          {{ article.title }}
        </nuxt-link>
      </li>
    </ul>
    <div v-if="'Error' in article">
      <h1>{{ article.Error }}</h1>
    </div>
    <div v-else>
      <nuxt-content :document="article" />
    </div>
  </div>
</template>

<script>
export default {
  async asyncData({ $content, params }) {
    try {
      const list = await $content("articles").only(["title", "slug"]).fetch();
      const article = await $content("articles", params.slug).fetch();
      return { article, list };
    } catch (e) {
      return {
        article: {
          Error: "Not Found",
        },
      };
    }

    // return { params };
  },
};
</script>

<style>

</style>