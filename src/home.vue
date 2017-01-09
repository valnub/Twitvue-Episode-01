<template lang="jade">
  div#app
    f7-views(navbar-through='')
      f7-view(main='', url='/', :dynamic-navbar='true')
        f7-navbar
          f7-nav-left
          f7-nav-center(sliding='') Twitvue
          f7-nav-right
        f7-pages#pages
          f7-page.navbar-fixed
            f7-searchbar(cancel-link='Cancel', placeholder='Search in items', :clear='true', v-on:change="onChange")
            f7-list.tweets(media-list='')
              f7-list-item(v-on:click='onClick(tweet)', link='/tweet/', v-for='tweet in tweets', :media='tweet.user.profile_image_html', :title='tweet.user.name', :subtitle='tweet.user.screen_name_at', :text='tweet.text')
</template>

<script>
import cb from './twitter.js';
import store from './store.js';

let self;

function addData(tweets) {
  for (let tweet of tweets) {
    tweet.user.screen_name_at = '@' + tweet.user.screen_name;
    tweet.user.profile_image_html = '<img src="' + tweet.user.profile_image_url + '">';
    
    let now = new Date();
    let createdAt = new Date(tweet.created_at);
    let timeDiff = dateDiff(createdAt, now);
    tweet.timeDiff = timeDiff + ' ago';
  }
}

function dateDiff(a, b) {
  let utc1 = Date.UTC(a.getFullYear(), a.getMonth(), a.getDate(), a.getUTCHours(), a.getUTCMinutes(), a.getUTCSeconds());
  let utc2 = Date.UTC(b.getFullYear(), b.getMonth(), b.getDate(), b.getUTCHours(), b.getUTCMinutes(), b.getUTCSeconds());

  let result = (utc2 - utc1) / (1000 * 60 * 60 * 24);
  let floor = Math.floor(result);
  if (floor > 0) return floor + "d";

  result *= 24;
  floor = Math.floor(result);
  if (floor > 0) return floor + "h";

  result *= 60;
  floor = Math.floor(result);
  if (floor > 0) return floor + "min";

  result *= 60;
  floor = Math.floor(result);
  if (floor > 0) return floor + "sec";
}

export default {
  name: 'app',
  data () {
    return {
      tweets : store.tweets
    }
  },
  created () {
    self = this;
  },
  methods : {
    onChange: function (event) {
      let term = event.target.value;
      if (!term) {
        self.$data.tweets.splice(0, self.$data.tweets.length);
      }
      else {
        window.f7.showPreloader();
        cb.__call(
            "search_tweets",
            "q=" + term,
            function (reply) {
                let result = reply.statuses;
                console.log(result);
                store.tweets = result;
                self.$data.tweets.length = 0;
                self.$data.tweets.push(...result);
                addData(self.tweets);
                window.f7.hidePreloader();
            },
            true // this parameter required
        );
      }
    },
    onClick: function (tweet) {
      store.selectedTweet = tweet;
    }
  }
}
</script>

<style lang="sass?indentedSyntax">
  .tweets
    .item-media
      img
        border-radius: 100%;
</style>
