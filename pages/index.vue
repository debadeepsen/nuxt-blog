<template>
  <div class="article">
    <ul>
      <li v-for="article in list" :key="article.slug">
        <nuxt-link :to="'./' + article.slug">
          {{ article.title }}
        </nuxt-link>
      </li>
    </ul>
  </div>
</template>


<script>
export default {
  async asyncData({ $content, params }) {
    try {
      const list = await $content("articles").only(["title", "slug"]).fetch();
      return { list };
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

