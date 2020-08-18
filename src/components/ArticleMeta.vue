<template>
  <div class="article-meta">
    <router-link
      :to="{ name: 'profile', params: { username: article.author.username } }"
    >
      <img :src="article.author.image" />
    </router-link>
    <div class="info">
      <router-link
        :to="{ name: 'profile', params: { username: article.author.username } }"
        class="author"
      >
        {{ article.author.username }}
      </router-link>
      <span class="date">{{ article.createdAt | date }}</span>
    </div>
    <rwv-article-actions
      v-if="actions"
      :article="article"
      :canModify="isCurrentUser()"
    ></rwv-article-actions>
    <button
      v-else
      class="btn btn-sm pull-xs-right"
      @click="toggleFavorite"
      :class="{
        'btn-primary': article.favorited,
        'btn-outline-primary': !article.favorited
      }"
    >
      <i class="ion-heart"></i>
      <span class="counter"> {{ article.favoritesCount }} </span>
    </button>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import RwvArticleActions from "@/components/ArticleActions";
import { FAVORITE_ADD, FAVORITE_REMOVE } from "@/store/actions.type";

export default {
  name: "RwvArticleMeta",
  metaInfo() {
    return {
      meta: [
        { charset: "utf-8" },
        { name: "viewport", content: "width=device-width, initial-scale=1" },
        {
          name: "title",
          content: `${this.article.title ? this.article.title : ""}`
        },
        {
          name: "description",
          content: `We are Serving from Rendertron - ${
            this.article.body ? this.article.body : ""
          }`
        },

        { name: "og:type", content: `website` },
        // { name: 'og:url', content: `We are Serving from Rendertron - ${this.article.body ? this.article.body : '' }` },
        {
          name: "og:title",
          content: `${this.article.title ? this.article.title : ""}`
        },
        {
          name: "og:description",
          content: `We are Serving from Rendertron - ${
            this.article.body ? this.article.body : ""
          }`
        },
        {
          name: "og:image",
          content: `https://cdn2.wpbeginner.com/wp-content/uploads/2019/01/choosethebestbloggingplatform.png`
        },

        { name: "twitter:card", content: `summary_large_image` },
        // { name: 'twitter:url', content: `We are Serving from Rendertron - ${this.article.body ? this.article.body : '' }` },
        {
          name: "twitter:title",
          content: `${this.article.title ? this.article.title : ""}`
        },
        {
          name: "twitter:description",
          content: `We are Serving from Rendertron - ${
            this.article.body ? this.article.body : ""
          }`
        },
        {
          name: "twitter:image",
          content: `https://cdn2.wpbeginner.com/wp-content/uploads/2019/01/choosethebestbloggingplatform.png`
        }
      ]
    };
  },
  components: {
    RwvArticleActions
  },
  props: {
    article: {
      type: Object,
      required: true
    },
    actions: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  computed: {
    ...mapGetters(["currentUser", "isAuthenticated"])
  },
  methods: {
    isCurrentUser() {
      if (this.currentUser.username && this.article.author.username) {
        return this.currentUser.username === this.article.author.username;
      }
      return false;
    },
    toggleFavorite() {
      if (!this.isAuthenticated) {
        this.$router.push({ name: "login" });
        return;
      }
      const action = this.article.favorited ? FAVORITE_REMOVE : FAVORITE_ADD;
      this.$store.dispatch(action, this.article.slug);
    }
  }
};
</script>
