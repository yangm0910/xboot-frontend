<template>
  <div class="message">
    <div class="left-contact">
      <Table height="800" :columns="columns1" :data="data2"></Table>
    </div>
    <div class="right-chatwindow">
      <div class="message-record">
        <div class="contactor">
          <h1>{{people}}</h1>
        </div>
        <ul v-for="msg in messageRecords" >
          <li class="single-msg" >{{msg}}</li>
        </ul>
      </div>
      <div class="message-input">
        <Input v-model="value" placeholder="Enter something..." style="width: 80%"/>
        <Button type="primary" @click="sendMsg" style="width: 20%">Primary</Button>
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
      username: "",
      value: "hello",
      websocket: null,
      people:"",
      messageRecords: [],
      columns1: [
        {
          title: "Action",
          key: "action",
          render: (h, params) => {
            return h(
              "el-button",
              {
                attrs: {
                  type: "text"
                },
                on: {
                  click: () => {
                    console.log(this.data2[params.index].name)
                    this.people=this.data2[params.index].name
                    this.init(this.data2[params.index].name);
                  }
                }
              },
              "Action"
            );
          }
        },
        {
          title: "Contact",
          key: "name"
        }
      ],
      data2: [{ name: "test" }, { name: "admin" }]
    };
  },

  methods: {
    init(rec) {
      //判断当前浏览器是否支持WebSocket
      let userInfo = JSON.parse(Cookies.get("userInfo"));
      let username = userInfo.username;
      this.username = rec;
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
      this.websocket.send(JSON.stringify({ "receiver": this.username, "content": this.value }));
    }
  }
};
</script>
<style lang="less">
@import "./messageManage.less";
</style>