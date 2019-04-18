<template>
  <div class="sorry">
     <ul v-for="item in messageRecords">
      <li>{{item}}</li>
    </ul>
    <input v-model="inputValue" id="text" type="text">
    <button @click="sendMsg">Send</button>
    <button @click>Close</button>
   
  </div>
</template>

<script>
import Cookies from "js-cookie";

export default {
  data() {
    return {
      inputValue: "hello",
      websocket: null,
      messageRecords: []
    };
  },
  mounted() {
    //判断当前浏览器是否支持WebSocket
    let userInfo = JSON.parse(Cookies.get("userInfo"));
      let username = userInfo.username;
    if ("WebSocket" in window) {
      this.websocket = new WebSocket("ws://127.0.0.1:8888/xboot/websocket/"+username);

      this.websocket.onopen = function(e) {
        console.log("link success");
      };
      this.websocket.onmessage = this.onMessage;
      this.websocket.onclose = this.onClose;
    } else {
      alert("Not support websocket");
    }
  },
  methods: {
    onMessage: function(e) {
      //收到消息
      console.log("reveive: " + e.data);
      this.messageRecords.push(e.data);

    },
    onClose: function() {
      console.log("colse");
    },
    sendMsg: function(msg) {
      this.websocket.send(this.inputValue);
    }
  }
};
</script>
<style lang="less">
.sorry {
  height: 500px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  .text {
    font-size: 20px;
    margin: 20px 0;
  }
}
</style>