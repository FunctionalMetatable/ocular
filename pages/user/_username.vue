<template>
  <div class="container">
    <Header pageName="user" :pageLink="`/user/${user}`" />
    <div class="margined">
      <Loading v-if="$fetchState.pending" />
      <div v-if="!$fetchState.pending">
        <h1 style="display: inline-block">{{ user }}</h1>
        <!-- todo: use classes instead of inline css -->
        <Status :user="user" style="display: inline" />
        <h2>{{ user }}'s posts</h2>
        <PostList :posts="posts" :loading="$fetchState.pending" @loadMore="loadMore()" :showLoadMore="showLoadMore"/>
      </div>
      <Footer />
    </div>
  </div>
</template>

<script>
export default {
  head() {
    return {
      title: `${this.user}'s scratch forum posts`
    }
  },
  data() {
    return {
      user: this.$route.params.username,
      page: 0,
      posts: {},
      info: {},
      showLoadMore: false
    };
  },
  methods: {
    async loadMore() {
      this.showLoadMore = false
      this.page++;

      var postsRes = await fetch(
        `https://scratchdb.lefty.one/v3/forum/user/posts/${this.user}/${this.page}`
      );
      var postData = await postsRes.json();
      if(Array.isArray(postData)){
        this.posts.push(...postData);
        this.showLoadMore = true 
      }
    },
  },
  async fetch() {
    var userInfoRes = await fetch(
      `https://scratchdb.lefty.one/v3/forum/user/info/${this.user}`
    );
    var userInfo = await userInfoRes.json();

    var userPostsRes = await fetch(
      `https://scratchdb.lefty.one/v3/forum/user/posts/${this.user}/${this.page}`
    );
    var userPosts = await userPostsRes.json();

    this.info = userInfo;
    this.posts = userPosts;

    this.showLoadMore = true
  },
  fetchOnServer: false,
};
</script>