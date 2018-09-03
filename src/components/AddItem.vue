<template>
  <div class="hello">
    <nav class="navbar is-primary" role="navigation" aria-label="dropdown navigation">
      <a class="navbar-item">
        <img src="https://bulma.io/images/bulma-logo.png" alt="Bulma: a modern CSS framework based on Flexbox" width="112" height="28">
      </a>
    </nav>
    <center>
      <div style="width: 40%">
          <b-field label="Item Name">
                  <b-input v-model="newItem.name"></b-input>
          </b-field>
          <b-field label="Scan QR">
                  <qrcode-reader @init="onInit" @decode="onDecode" :paused="paused"></qrcode-reader>
                  
          </b-field>
          <b-field label="Item Sn">
              <b-input v-model="newItem.sn"></b-input>
          </b-field>
          <b-field label="Item Count">
                  <b-input type="number" v-model="newItem.count"></b-input>
          </b-field>
          <button class="button is-success" @click="addItem">ADD ITEM</button>
    </div>
    </center>

  </div>
</template>

<script>
import { db } from "../mixins/firebase.js";

let items = db.ref("items");

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      name: "fast",
      paused : false,
      errorMessage : '',
      newItem: {
        id: "",
        name: "",
        sn: "",
        count: 0
      }
    };
  },
  methods: {
    addItem: function() {
      items.push(this.newItem);
      // this.newItem.title = "";
      // this.newItem.author = "";
      // this.newItem.count = 0;
      this.newItem = {};
      this.$router.push({ name: "home" });
    },
    onDecode: function(content) {
      this.newItem.sn = content.split('=')[1];
      console.log(this.newItem.sn);
      
      this.paused = true;
    },
    onInit: function(promise) {
      promise.then(() => {
        console.log('Successfully initilized! Ready for scanning now!')
      })
      .catch(error => {
        if (error.name === 'NotAllowedError') {
          this.errorMessage = 'Need access to your camera'
        } else if (error.name === 'NotFoundError') {
          this.errorMessage = 'No camera on your device?'
        } else if (error.name === 'NotSupportedError') {
          this.errorMessage = 'Seems like this page is served in non-secure context (HTTPS, localhost or file://)'
        } else if (error.name === 'NotReadableError') {
          this.errorMessage = 'Couldn\'t access your camera. Is it already in use?'
        } else if (error.name === 'OverconstrainedError') {
          this.errorMessage = 'Constraints don\'t match any installed camera. Did you asked for the front camera although there is none?'
        } else {
          this.errorMessage = 'UNKNOWN ERROR: ' + error.message
        }
      })
    }
  
  },
  components: {}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
</style>
