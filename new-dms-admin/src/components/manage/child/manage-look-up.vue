<template>
  <div id="look-up-container">
    <h1>제목: {{title}}</h1>
    <div v-html="content"></div>
  </div>
</template>

<script>
export default {
  created: function () {
    this.$axios.get('/v2/post/' + this.category + '/' + this.lookUpPostId,
      {
        headers: {
          'Authorization': 'JWT ' + this.$getCookie('JWT')
        }
      })
    .then(response => {
      this.content = response.data.content
      this.title = response.data.title
    })
    .catch(e => {
      console.log('ERROR ==> ' + e)
    })
  },
  props: ['lookUpPostId', 'category'],
  data: function () {
    return {
      content: '',
      title: ''
    }
  }
}
</script>

<style>

</style>
