<template>
  <div class="message">
    <div class="left-contact">
      <Table height="800" :columns="columns1" :data="data2"></Table>

      <!-- <ul>
        <li>111</li>
        <li>222</li>
      </ul>-->
    </div>
    <div class="right-chatwindow">
      <div class="message-record">
        <div class="contactor">
          <h1>people</h1>
        </div>
        <ul>
          <li class="single-msg">hi</li>

          <li class="single-msg">hi</li>

          <li class="single-msg">hi</li>
        </ul>
      </div>
      <div class="message-input">
        <Input v-model="value" placeholder="Enter something..." style="width: 80%"/>
        <Button type="primary" @click="sendMsg"  style="width: 20%">Primary</Button>
      </div>
    </div>
    <!-- <Table height="200" :columns="columns1" :data="data2"></Table>
    <ul v-for="item in messageRecords">
      <li>{{item}}</li>
    </ul>
    <div class="main">
      <input v-model="inputValue" id="text" type="text">
      <button @click="sendMsg">Send</button>
    </div>-->
  </div>
</template>

<script>
import Cookies from "js-cookie";
export default {
  data() {
    return {
      value: "hello",
      websocket: null,
      messageRecords: [],
      columns1: [
        {
          title: "Contact",
          key: "name"
        }
      ],
      data2: [{ name: "test" }, { name: "test2" }]
    };
  },

  methods: {
    init() {
      //判断当前浏览器是否支持WebSocket
      let userInfo = JSON.parse(Cookies.get("userInfo"));
      let username = userInfo.username;
      if ("WebSocket" in window) {
        this.websocket = new WebSocket(
          "ws://127.0.0.1:8888/xboot/websocket/" + username
        );

        this.websocket.onopen = function(e) {
          console.log("link success");
        };
        this.websocket.onmessage = this.onMessage;
        this.websocket.onclose = this.onClose;
      } else {
        alert("Not support websocket");
      }
    },
    onMessage: function(e) {
      //收到消息
      console.log("reveive: " + e.data);
      this.messageRecords.push(e.data);
    },
    onClose: function() {
      console.log("colse");
    },
    sendMsg: function(msg) {
      this.websocket.send(this.value);
    }
  }
};
</script>
<style lang="less">
@import "./messageManage.less";
</style>