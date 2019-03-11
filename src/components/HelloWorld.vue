<template>
  <div>
      <h1>Test KT TEam</h1>
       <transition-group name="list" tag="p">
      <div v-for="item in paginatedData"
        :key="item.data.title"
        class="listBox">
        <h1 class="listTitle">{{item.data.title}}</h1>
        <p>{{item.data.author}}</p>
        <img :src="item.data.url" class="listImg">
      </div>
      </transition-group>
     <button @click="prevPage" :disabled="pageNumber === 0">
    Previous
  </button>
  <button @click="nextPage" :disabled="pageNumber >= pageCount -1">
    Next
  </button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      postList: null,
      meList: [],
      pageNumber: 0,
      size: 5,
      token: '265112990511-vwEJeiCIww1XIpPx6R76dP8yWAI',
      codes: null
    }
  },
  methods: {
    nextPage () {
      this.pageNumber++
    },
    prevPage () {
      this.pageNumber--
    },
    getList () {
      let params = new URLSearchParams()
      params.append('limit', '100')

      this.$http({
        method: 'GET',
        url: 'https://oauth.reddit.com/r/cat/hot',
        headers: {
          Authorization: 'bearer ' + this.token
        }
      })
        .then(response => {
          this.meList = response.data.data.children
        })
        .catch(error => {
          if (error.response.status === 401) {
            this.refreshToken()
          }
        })
    },
    refreshToken () {
      let params = new URLSearchParams()
      params.append('grant_type', 'refresh_token')
      params.append('refresh_token', this.token)

      this.$http({
        method: 'POST',
        url: 'https://www.reddit.com/api/v1/access_token',
        data: params,
        auth: {
          username: 'I3IybKQMS4qFKw',
          password: 'QDW0MSuY2XV735ZQ1h4aLDfUg1Y'
        }
      })
        .then(response => {
          this.token = response.data.access_token
        })
    }
  },
  mounted () {
    this.getList()
  },
  computed: {
    pageCount () {
      let l = this.meList.length
      let s = this.size
      return Math.ceil(l / s)
    },
    paginatedData () {
      const start = this.pageNumber * this.size
      const end = start + this.size
      return this.meList.slice(start, end)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.listBox{

}

.listImg{
  height: auto;
  width: 30%;
}

button{
  width:100px;
  height:40px;
  background-color:#eef;
}

button:hover{
  cursor:pointer;
}
button:hover:disabled{
  cursor:not-allowed;
}

.list-enter-active, .list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to /* .list-leave-active до версии 2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}
</style>
