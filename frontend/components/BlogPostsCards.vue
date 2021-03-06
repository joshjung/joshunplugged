<template>
  <div class="blog-post-cards">
    <div v-for="blogPost in blogPostsSorted"
         :style="{'background-color': blogPost.background_color}"
         :key="blogPost.id">
      <div class="post-card">
          <div class="image-container">
            <image-sized :image="blogPost.image_header" size="small" />
          </div>
          <div class="text-block">
            <div v-if="blogPost.postCategory" class="uk-text-uppercase">{{ blogPost.postCategory.name }}</div>
            <nuxt-link :to="{ name: 'posts-slug', params: {slug: blogPost.slug} }"
                         class="story-title">{{ blogPost.title }}</nuxt-link>
            <div class="date-count-block">
              <div class="date"
                  v-if="blogPost.manually_published_at">{{ publishedAtFormatted(blogPost.manually_published_at) }}</div>
              <div class="count"
                   v-if="!!blogPost.blog_series">{{blogPost.blog_series_order}} of {{blogPost.blog_series.blog_posts.length}}
                <nuxt-link v-if="showSeriesLink"
                             :to="{ name: 'series-slug', params: {slug: blogPost.blog_series.slug} }"
                             class="story-title">in {{blogPost.blog_series.title}}</nuxt-link>
              </div>
            </div>
            <div class="description"
                 v-if="blogPost.show_description && blogPost.description"
                 v-html="$md.render(blogPost.description)"
                 :style="{ 'color': blogPost.foreground_color }">
            </div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
  import moment from 'moment';
  import imagesQuery from "~/apollo/queries/image/images.gql";
  import './BlogPostsCards.scss';
  import ImageSized from '~/components/images/ImageSized';

  export default {
    components: {ImageSized},
    data: function() {
      return {
        images: []
      }
    },
    methods: {
      publishedAtFormatted(published_at) {
        if (!published_at) {
          return '';
        }

        return moment(published_at).format('MMMM Do YYYY');
      }
    },
    computed: {
      blogPostsSorted() {
        if (this.sortKey === 'published_at') {
          return this.blogPosts.sort((a, b) => {
            if (a.manually_published_at && b.manually_published_at) {
              if (a.manually_published_at === b.manually_published_at) {
                return a.blog_series_order < b.blog_series_order ? 1 : -1;
              }

              return new Date(a.manually_published_at).getTime() > new Date(b.manually_published_at).getTime() ? -1 : 1;
            } else if (a.manually_published_at) {
              return 1;
            }
            return -1;
          });
        } else if (this.sortKey === 'blog_series_order') {
          return this.blogPosts.sort((a, b) => a.blog_series_order > b.blog_series_order ? 1 : -1);
        }

        return this.blogPosts;
      }
    },
    props: {
      blogPosts: Array,
      sortKey: {
        default: 'published_at',
        type: String
      },
      showSeriesLink: {
        type: Boolean,
        default: true
      }
    },
    apollo: {
      images: {
        prefetch: false,
        query: imagesQuery,
        variables() {
          return { target: 'default_blog_post_background' };
        }
      }
    }
  };
</script>
