<template lang="jade">
  f7-page(hide-bars-on-scroll='')
    f7-navbar(back-link='Back', sliding='')
    .card.facebook-card.tweet
      .card-header.no-border
        .facebook-avatar
          img(:src='tweet.user.profile_image_url', width='34', height='34')
        .facebook-name {{tweet.user.name}}
        .facebook-date {{tweet.timeDiff}}
      .card-content
        {{tweet.text}}
        img(v-if="tweet.entities.media", :src='tweet.entities.media[0].media_url', width='100%')
      .card-footer.no-border
        a.link(href='#') Like
        a.link(href='#') Comment
        a.link(href='#') Share

</template>

<script>
import store from './store.js';

export default {
  name: 'tweet',
  data () {
    return {
      tweet: store.selectedTweet
    }
  }
}
</script>

<style lang="sass?indentedSyntax">
  .tweet
    .card-content
      padding: 15px;
</style>
