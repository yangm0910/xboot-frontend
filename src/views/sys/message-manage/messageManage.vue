<template>
  <div class="message">
    <div class="left-contact">
      <Table height="800" :columns="columns1" :data="data2"></Table>
    </div>
    <div class="right-chatwindow">
      <div class="message-record">
        <div class="contactor">
          <h1>{{people.username}}</h1>
        </div>
        <ul>
          <li v-for="item in messageRecords" :key="item.id">
            <span class="single-msg-receive" v-if="item.send==people.username">{{item.msg}}</span>
            <span class="single-msg-send" v-else>{{item.msg}}</span>
          </li>
        </ul>
      </div>

      <div class="message-input">
        <Input v-model="value" placeholder="Enter something..." style="width: 80%"/>
        <Button type="primary" @click="sendMsg" style="width: 20%">Primary</Button>
      </div>
    </div>
    <!-- <Table height="200" :columns="columns1" :data="data2"></Table>
    
    <div class="main">
      <input v-model="inputValue" id="text" type="text">
      <button @click="sendMsg">Send</button>
    </div>-->
  </div>
</template>

<script>
import Cookies from "js-cookie";
import { apiGetAllUser, apiSendMessage } from "@/api/index";
export default {
  data() {
    return {
      username: "",
      value: "hello",
      websocket: null,
      websockets: [],
      people: {},
      messageRecords: [],
      columns1: [
        {
          title: "Image",
          key: "avatar",
          render: (h, params) => {
            return h(
              "el-a",
              {
                attrs: {
                  herf: this.data2[params.index].avatar
                }
                // on: {
                //   click: () => {
                //     console.log(this.data2[params.index].username);
                //     this.people.username = this.data2[params.index].username;
                //     this.people.id = this.data2[params.index].id;
                //     // this.init(this.data2[params.index].id);
                //   }
                // }
              },
              <a herf="this.data2[params.index].avatar" />
            );
          }
        },
        {
          title: "Friends",
          key: "username",
          render: (h, params) => {
            return h(
              "el-button",
              {
                attrs: {
                  type: "text"
                },
                on: {
                  click: () => {
                    console.log(this.data2[params.index].username);
                    this.people.username = this.data2[params.index].username;
                    this.people.id = this.data2[params.index].id;
                    // this.init(this.data2[params.index].id);
                  }
                }
              },
              this.data2[params.index].username
            );
          }
        }
      ],
      data2: [{ username: "test" }, { username: "admin" }]
    };
  },

  methods: {
    init(rec) {
      //判断当前浏览器是否支持WebSocket
      let userInfo = JSON.parse(Cookies.get("userInfo"));
      let username = userInfo.username;
      let userId = userInfo.id;
      let sid = userId + "-" + rec;
      // this.username = rec;
      if ("WebSocket" in window) {
        let thisWebsocket = null;
        thisWebsocket = new WebSocket("ws://127.0.0.1:8888/websocket/" + sid);

        thisWebsocket.onopen = function(e) {
          console.log("link success");
        };
        thisWebsocket.onmessage = this.onMessage;
        thisWebsocket.onclose = this.onClose;
        this.websocket = thisWebsocket;
        this.websockets.push(thisWebsocket);
      } else {
        alert("Not support websocket");
      }
    },
    onMessage: function(e) {
      //收到消息
      let userInfo = JSON.parse(Cookies.get("userInfo"));
      let username = userInfo.username;
      console.log("reveive: " + e.data);
      let index = this.messageRecords.id || 0;
      this.messageRecords.push({
        receiver: username,
        send: this.people.username,
        msg: e.data,
        id: ++index
      });
    },
    onClose: function() {
      console.log("colse");
    },
    sendMsg: function(msg) {
      let userInfo = JSON.parse(Cookies.get("userInfo"));
      let username = userInfo.username;
      let userId = userInfo.id;
      let receiver = this.people.id + "-" + userId;
      //send through api
      let index = this.messageRecords.id || 0;
      console.log("send:", this.value);
      apiSendMessage({ id: receiver, message: this.value }).then(res => {
        res.code == 200 &&
          this.messageRecords.push({
            receiver: this.people.username,
            send: username,
            msg: this.value,
            id: ++index
          });
      });
      // this.websocket.send(
      //   JSON.stringify({ receiver: this.username, content: this.value })
      // );
    },
    getAllUser: function() {
      apiGetAllUser({ pageSize: 10, pageNumber: 1 }).then(res => {
        console.log(res.result.content);
        this.data2 = res.result.content;
        //创建连接
        res.result.content.map(item => {
          this.init(item.id);
        });
      });
    }
  },
  mounted() {
    this.getAllUser();
  }
};
</script>
<style lang="less">
@import "./messageManage.less";
</style>