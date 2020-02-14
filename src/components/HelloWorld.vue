<template>
  <div class="hello">
    <h1>Messages</h1>
    <ul v-if="username != null">
      <li  v-for="his in history" :key="his.messageId">
       <h1> {{his.message}}</h1>
        <div v-if="his.sender.isActive == true"><img :src="his._sender.profileUrl"  alt="Smiley face" height="42" width="42" align="right" style="border-style: groove; border-radius: 100px; border-size:200px; border-color:green"></div>
        <div v-else><img :src="his._sender.profileUrl"  alt="Smiley face" height="42" width="42" align="right" style="border-style: groove; border-radius: 100px; border-size:200px; border-color:red"></div>
        <h2 style="text-align:right; ">{{his._sender.userId}}</h2>
      </li>
    </ul>
    <h1>
      <input type="text" placeholder="Enter a message to send" v-model="info">
      <input type="text" placeholder="Enter username" v-model="username">
      <input type="button" @click="sendMessage" value="SEND">
    </h1>
    <h1>
      <h6>If you can't see messages enter a username</h6>
      <h1>Participants</h1>
      <ul>
      <li v-for="par in participants" :key="par">
        <div v-if="par.isActive == true"><img :src="par.profileUrl" align="right" alt="Smiley face" height="100" width="100" style="border-style: groove; border-radius: 50%; border-size:200px; border-color:green"></div>
      <div v-else><img :src="par.profileUrl"  alt="Smiley face" align="right" height="100" width="100" style="border-style: groove; border-radius: 50%; border-size:200px; border-color:red;"></div>
       <h1 style="text-align:left; font-size:50px;"> {{par.userId}} </h1>
      <!--<img :src="par.profileUrl" alt="Smiley face" height="42" width="42"><!-->
      </li>
    </ul>
    </h1>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      info: '',
      history: [],
      participants: [],
      username: ''
    }
  },
  mounted () {
    setTimeout(this.getMessages(), 100)
    setTimeout(this.getParticipants(), 100)
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
      // console.log('this info' + this.info)
      if (this.username.length === 0) { return alert('Enter username buddy and message') }
      var USER_ID = this.username// (Math.floor(Math.random() * (10000 - 0) + 0)).toString()

      sb.connect(USER_ID, function (user, error) {
        if (error) {
          console.log(error)
        }
        console.log(the.username)
        user.nickname = the.username
      })

      const params = new sb.UserMessageParams()
      if (this.info.length === 0) { return alert('Enter something buddy') }
      params.message = this.info
      this.info = ' '

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
      the.getParticipants()
    },
    getMessages () {
      var the = this
      const SendBird = require('sendbird')
      const ID = 'D579A319-940D-4832-ACBD-C0C0A98FCDEA'
      const URL = 'sendbird_open_channel_315_c6074e0129c90a50485eb6f4cdfaa4a1027d4c90'
      var sb = new SendBird({ appId: ID })
      var his = this.history
      var USER_ID = this.username// (Math.floor(Math.random() * (10000 - 0) + 0)).toString()

      sb.connect(USER_ID, function (user, error) {
        if (error) {
          console.log(error)
        }
        user.nickname = the.username
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
      the.getParticipants()
    },
    getParticipants () {
      var the = this
      const SendBird = require('sendbird')
      const ID = 'D579A319-940D-4832-ACBD-C0C0A98FCDEA'
      const URL = 'sendbird_open_channel_315_c6074e0129c90a50485eb6f4cdfaa4a1027d4c90'
      var sb = new SendBird({ appId: ID })
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
          var participantListQuery = openChannel.createParticipantListQuery()

          participantListQuery.next(function (participantList, error) {
            if (error) {
              return
            }
            the.participants = participantList
            console.log(participantList)
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
  padding: 10px;
}
li {
  display: block;
  margin: 0 0;
  padding: 5px;
  border-style: solid;
  text-align: left;
}
a {
  color: #42b983;
}

input[type=text] {
  width: 50%;
  padding: 12px 20px;
  margin: 8px 0;
  box-sizing: border-box;
  text-align: center;
  font-size: 70%;
}
input[type=button] {
  width: 50%;
  padding: 12px 20px;
  margin: 8px 0;
  box-sizing: border-box;
  font-family: monospace;
  letter-spacing: 10px;
  background-color: #42b983;
  font-size: 200%;
  text-align: center;
}

</style>
