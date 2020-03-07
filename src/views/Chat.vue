<template>
  <div class="booker">
    <nav-bar :name="this.username" :avatar="this.avatar" />
    <div class="chat">
        <div class="container">
          <div class="msg-header">
              <div class="active">
                  <h5>#General
                <span class="badge badge-danger" v-if="unreadCount">
                  {{ unreadCount }}
                </span>
              </h5>
              </div>
          </div>
          <div class="chat-page">
              <div class="msg-inbox">
                  <div class="chats" id="chats">
                      <div class="msg-page" id="msg-page">
                        <div
                          v-if="loadingMessages"
                          class="loading-messages-container"
                        >
                          <spinner :size="100"/>
                          <span class="loading-text">
                            Loading Messages
                          </span>
                        </div>
                        <div class="text-center img-fluid empty-chat" v-else-if="!groupMessages.length" >
                          <div class="empty-chat-holder">
                            <img src="../assets/empty-state.svg" class="img-res" alt="empty chat image">
                          </div>
                          <div>
                            <h2> No new message? </h2>
                            <h6 class="empty-chat-sub-title">
                              Send your first message below.
                            </h6>
                          </div>
                        </div>
                        <div v-else>
                          <div v-for="(message, i) in groupMessages" :key="`${i}-${message.id}`">
                            <div class="received-chats" v-if="message.sender.uid != uid">
                                <div class="received-chats-img">
                                  <img v-bind:src="message.sender.avatar" alt="" class="avatar">
                                </div>
                                <div class="received-msg">
                                    <div class="received-msg-inbox" style="position: relative">
                                        <p><span>{{ message.sender.uid }}</span><br>{{ message.data.text }}</p>
                                    </div>
                                </div>
                              </div>

                            <div class="outgoing-chats" v-else>
                                  <div class="outgoing-chats-msg" style="position: relative;">
                                      <p>{{ message.data.text }}</p>

                                      <div v-if="message.sent" style="position: absolute; right: 0; bottom: 0;padding: 10px 10px 0 0;">
                                         <svg xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="23" height="23" viewBox="0 0 226 226" style=" fill:#000000;">
                                            <g fill="none" fill-rule="nonzero" stroke="none" stroke-width="1" stroke-linecap="butt" stroke-linejoin="miter" stroke-miterlimit="10" stroke-dasharray
                                              stroke-dashoffset="0" font-family="none" font-weight="none" font-size="none" text-anchor="none" style="mix-blend-mode: normal">
                                              <path d="M0,226v-226h226v226z" fill="none" />
                                              <g fill="#2ecc71">
                                                <path d="M212.68483,42.375l-109.1015,109.90192l-43.17071,-43.98525l-13.32929,13.43758l56.5,57.18742l122.41667,-123.396z"/>
                                              </g>
                                            </g>
                                          </svg>
                                      </div>

                                        <div v-if="message.delivered" style="position: absolute; right: 0; bottom: 0;padding: 10px 10px 0 0;">
                                         <svg xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="23" height="23" viewBox="0 0 226 226" style=" fill:#000000;">
                                            <g fill="none" fill-rule="nonzero" stroke="none" stroke-width="1" stroke-linecap="butt" stroke-linejoin="miter" stroke-miterlimit="10" stroke-dasharray
                                              stroke-dashoffset="0" font-family="none" font-weight="none" font-size="none" text-anchor="none" style="mix-blend-mode: normal">
                                              <path d="M0,226v-226h226v226z" fill="none" />
                                              <g fill="#2ecc71">
                                                <path d="M212.68483,42.375l-109.1015,109.90192l-43.17071,-43.98525l-13.32929,13.43758l56.5,57.18742l122.41667,-123.396z"/>
                                                <path d="M165.6015,42.375l-109.1015,109.90192l-43.17071,-43.98525l-13.32929,13.43758l56.5,57.18742l122.41667,-123.396z"/>
                                              </g>
                                            </g>
                                          </svg>
                                      </div>

                                      <div v-tooltip="{content: readers,loadingContent: '<i>Loading...</i>',}" v-else-if="message.read" style="position: absolute; right: 0; bottom: 0;padding: 10px 10px 0 0;">
                                         <svg xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="23" height="23" viewBox="0 0 226 226" style=" fill:#000000;">
                                          <g fill="none" fill-rule="nonzero" stroke="none" stroke-width="1" stroke-linecap="butt" stroke-linejoin="miter" stroke-miterlimit="10" stroke-dasharray
                                            stroke-dashoffset="0" font-family="none" font-weight="none" font-size="none" text-anchor="none" style="mix-blend-mode: normal">
                                            <path d="M0,226v-226h226v226z" fill="none" />
                                            <g>
                                              <path d="M212.68483,42.375l-109.1015,109.90192l-43.17071,-43.98525l-13.32929,13.43758l56.5,57.18742l122.41667,-123.396z" fill="#0c9eff"/>
                                              <path d="M165.6015,42.375l-109.1015,109.90192l-43.17071,-43.98525l-13.32929,13.43758l56.5,57.18742l122.41667,-123.396z" fill="#06a3ff"/>
                                            </g>
                                          </g>
                                        </svg>
                                      </div>
                                  </div>
                                  <div class="outgoing-chats-img">
                                      <img v-bind:src="message.sender.avatar" alt="" class="avatar">
                                  </div>
                              </div>
                          </div>
                        </div>
                      </div>
                  </div>
              </div>

              <div class="msg-bottom">
                <form class="message-form" v-on:submit.prevent="sendGroupMessage">
                  <div class="input-group">
                    <input type="text" class="form-control message-input" placeholder="Type something" v-model="chatMessage" required>
                    <spinner
                      v-if="sendingMessage"
                      class="sending-message-spinner"
                      :size="30"
                    />
                  </div>
                </form>
              </div>
          </div>
      </div>
    </div>
  </div>
</template>


<script>
import { CometChat } from "@cometchat-pro/chat";
import NavBar from "../components/NavBar";
import Spinner from "../components/Spinner"

export default {
  name: "home",
  components: {
    NavBar,
    Spinner
  },
  data() {
    return {
      username: "",
      avatar: "",
      uid: "",
      messageId: "",
      sendingMessage: false,
      messageSent: false,
      chatMessage: "",
      loggingOut: false,
      groupMessages: [],
      loadingMessages: false,
      unreadCount: 0,
      readers: [],
    }
  },

  mounted() {
    this.loadingMessages = true;
    this.fetchMessageHistoryForGroup();

  },
  created() {
    this.getLoggedInUser();

    var listenerID = "UNIQUE_LISTENER_ID"
    CometChat.addMessageListener(
      listenerID,
      new CometChat.MessageListener({
        onTextMessageReceived: textMessage => {
          console.log("Text message received successfully", textMessage);
          
          if (this.uid !== textMessage.sender.uid) {
              this.groupMessages = [...this.groupMessages, textMessage]
          }

          this.loadingMessages = false;
          this.$nextTick(() => {
            this.scrollToBottom();
          })
        },
        onMessagesDelivered: messageReceipt => {
          console.log("MessageDeliverd", { messageReceipt });

          const content = this.groupMessages.map(message => {
            if (message.id === messageReceipt.messageId) {
              return {...message, delivered: true, read: false, sent: false}
            }
            return message;
          });
          this.groupMessages = content;

          CometChat.markAsRead(messageReceipt.messageId, messageReceipt.receiver, messageReceipt.receiverType);
        },
        onMessagesRead: messageReceipt => {
          console.log("MessageRead", { messageReceipt });

          const updatedContent = this.groupMessages.map(message => {
            if (message.id === messageReceipt.messageId) {
              return {...message, read: true, delivered: false, sent: false}
            }
            return message;
          });
          this.groupMessages = updatedContent;

          this.updateMessageReaders(messageReceipt);
        }
      })
    );
  },
  methods: {
    updateMessageReaders(messageReceipt) {
      let messageReaders = [];
      CometChat.getMessageReceipts(messageReceipt.messageId).then(receipts => {
      console.log("Message details receipt fetched:", receipts);
                
        receipts.forEach(data => {
          messageReaders = [...messageReaders, data.sender.uid]
        });
        this.readers = [...new Set(messageReaders)];
                  
      }, error => {
        console.log("Error in getting messag details ", error)
      });
    },
    getLoggedInUser() {
      CometChat.getLoggedinUser().then(
        user => {
          this.username = user.name;
          this.avatar = user.avatar;
          this.uid = user.uid;
        },
        error => {
          this.$router.push({
            name: "homepage"
          });
          console.log(error);
        }
      );
    },
    sendGroupMessage() {
      this.sendingMessage = true;
      let receiverID = process.env.VUE_APP_COMMETCHAT_GUID;
      let messageText = this.chatMessage;
      let receiverType = CometChat.RECEIVER_TYPE.GROUP;
      let globalContext = this;
      let textMessage = new CometChat.TextMessage(
        receiverID,
        messageText,
        receiverType
      );
      CometChat.sendMessage(textMessage).then(
        message => {
          console.log("Message sent successfully:", message);
          this.chatMessage = "";
          this.sendingMessage = false;
          this.messageSent = true;
          const newMessage = {...message, read: false, delivered: false, sent: true}
          this.groupMessages = [...globalContext.groupMessages, newMessage];
          this.$nextTick(() => {
            this.scrollToBottom()
          })
        },
        error => {
          console.log("Message sending failed with error:", error);
        }
      );
    },
    fetchMessageHistoryForGroup() {
      var GUID = process.env.VUE_APP_COMMETCHAT_GUID;
      var limit = 100;

      var messagesRequest = new CometChat.MessagesRequestBuilder()
        .setLimit(limit)
        .setGUID(GUID)
        .build();

      messagesRequest.fetchPrevious().then(
        messages => {
          console.log("Message list fetched:", messages);

          // this.groupMessages = messages;
          this.loadingMessages = false;

          const updatedContent = messages.map(message => {

            CometChat.getMessageReceipts(message.id).then(
            receipts => {
              // console.log(receipts.length);
              if (receipts.length > 0) {
                return {...message, read: true, delivered: false}
              } else {
                //  console.log("Message details fetched:", receipts);
                return {...message, read: false, delivered: true,}
              }
            },
            error => {
              console.log("Error in getting messag details ", error);
            }
           );
           return message;
          });

          this.groupMessages = updatedContent;


          this.$nextTick(() => {
            this.scrollToBottom();
          })
        },
        error => {
          console.log("Message fetching failed with error:", error);
        }
      );
    },
    scrollToBottom() {
      const chat = document.getElementById("msg-page");
      chat.scrollTo(0, chat.scrollHeight + 30);
    }
  }
}
</script>