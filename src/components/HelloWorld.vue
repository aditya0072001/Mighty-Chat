<template>
  <div class="hello">
    <ul >
      <li v-for="his in history" :key="his.messageId">
        {{his.message}}   (send by userId->{{his._sender.userId}})
      </li>
    </ul>
    <h1>
      <input type="text" v-model="info">
      <input type="button" @click="sendMessage" value="SEND">
    </h1>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      info: '',
      history: []
    }
  },
  mounted () {
    setTimeout(this.getMessages(), 100)
  },
  methods: {
    sendMessage () {
      // set(his){
      // this.getGlobal(his)
      // };
      var the = this
      const SendBird = require('sendbird')
      const ID = 'D579A319-940D-4832-ACBD-C0C0A98FCDEA'
      const URL = 'sendbird_open_channel_315_c6074e0129c90a50485eb6f4cdfaa4a1027d4c90'
      var sb = new SendBird({ appId: ID })
      // var his = this.history
      const params = new sb.UserMessageParams()
      // console.log('this info' + this.info)
      if (this.info.length === 0) { return alert('Enter something buddy') }
      params.message = this.info
      this.info = ''

      var USER_ID = (Math.floor(Math.random() * (10000 - 0) + 0)).toString()

      sb.connect(USER_ID, function (user, error) {
        if (error) {
          console.log(error)
        }
      })
      sb.OpenChannel.getChannel(URL, function (openChannel, error) {
        if (error) {
          console.log(error)
          return
        }

        openChannel.enter(function (response, error) {
          if (error) {
            console.log(error)
            return
          }
          openChannel.sendUserMessage(params, function (message, error) {
            if (error) {

            }
            console.log(message)
          })
        })
      })
      the.getMessages()
    },
    getMessages () {
      var the = this
      const SendBird = require('sendbird')
      const ID = 'D579A319-940D-4832-ACBD-C0C0A98FCDEA'
      const URL = 'sendbird_open_channel_315_c6074e0129c90a50485eb6f4cdfaa4a1027d4c90'
      var sb = new SendBird({ appId: ID })
      var his = this.history
      var USER_ID = (Math.floor(Math.random() * (10000 - 0) + 0)).toString()

      sb.connect(USER_ID, function (user, error) {
        if (error) {
          console.log(error)
        }
      })
      sb.OpenChannel.getChannel(URL, function (openChannel, error) {
        if (error) {
          console.log(error)
          return
        }

        openChannel.enter(function (response, error) {
          if (error) {
            console.log(error)
            return
          }

          var messageListQuery = openChannel.createPreviousMessageListQuery()
          // messageListQuery.limit = 20
          messageListQuery.reverse = false
          messageListQuery.load(function (messageList, error) {
            if (error) {
              console.log(error)
              return
            }
            his = messageList
            the.history = his
            console.log(messageList)
          })
        })
      })
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
  display: block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
