<template>
  <Loader :loading="loading || !bookReviews">
    <div class="page book-reviews" v-if="!!bookReviews">
      <div class="hero book-reviews-hero"
           style="background-image: url(https://joshoncode.s3.us-east-2.amazonaws.com/library_shelves_3b1e5330c5.jpg?291148.354999983);">
      </div>
      <div class="contents">
        <div class="header">
          <div class="content-title-block">
            <div class="content-title">Book Reviews</div>
            <div class="content-description"><TextByTag tag="book_reviews_page"></TextByTag></div>
          </div>
        </div>
        <div class="content-section">
          <BookReviewPostCards :bookReviews="bookReviews"
                               v-if="bookReviews && bookReviews.length" />
          <div v-else>
            <h2>No public posts in this series yet.</h2>
          </div>
        </div>
      </div>
    </div>
  </Loader>
</template>

<script>
  import bookReviewsQuery from "~/apollo/queries/bookReview/bookReviews.gql";
  import BookReviewPostCards from "~/components/BookReviewPostCards";
  import moment from 'moment';
  import Loader from "~/components/Loader";
  import TextByTag from "~/components/TextByTag";
  import { publishedAt } from '~/util/dates';

  export default {
    data() {
      return {
        bookReviews: undefined,
        loading: 0
      };
    },
    components: {
      BookReviewPostCards,
      Loader,
      TextByTag
    },
    computed: {
      publishedAtFormatted(date) {
        return publishedAt(date);
      }
    },
    apollo: {
      bookReviews: {
        prefetch: false,
        loadingKey: "loading",
        query: bookReviewsQuery,
        variables() {
          return { id: parseInt(this.$route.params.id) };
        }
      }
    }
  };
</script>
