<template>
  <div class="footer-backdrop">
    <Header />
    <div class="body"
         :style="{ 'margin-top': bodyMarginTop + 'px' }">
      <nuxt />
    </div>
    <Footer />
  </div>
</template>

<script>
  import blogPostCategoriesQuery from "~/apollo/queries/category/blogPostCategories";
  import Header from "~/layouts/Header";
  import Footer from "~/layouts/Footer";
  import '~/styles/base.scss';
  import './default.scss';

  export default {
    data() {
      return {
        postCategories: [],
        bodyMarginTop: 1
      };
    },
    mounted() {
      setInterval(() => {
        const header = document.getElementById('page-header');
        this.bodyMarginTop = header.offsetHeight;
        this.$forceUpdate();
      }, 500);

      // Setup Apollo Login JWT token, on startup
      if (this.$auth.strategy.token.get()) {
        this.$apolloHelpers.onLogin(this.$auth.strategy.token.get().replace('Bearer ', ''));
      }
    },
    beforeDestroy() {
      window.removeEventListener('resize', this.onResize)
    },
    components: {
      Header,
      Footer
    },
    apollo: {
      blogPostCategories: {
        prefetch: false,
        query: blogPostCategoriesQuery
      }
    }
  };
</script>
