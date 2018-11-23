<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div class="messagebox">
      <b-form-textarea textarea v-model="tweet" v-on:input="tweetChange()" placeholder="Enter Message to be encrypted" :rows="2"></b-form-textarea >
    </div>
    <div class="algo">
      <b-form-select v-model="algo" v-on:input="tweetChange()" class="mb-3" size="sm">
        <option v-for="algo in algos" v-bind:value="algo">
          {{ algo }}
        </option>
      </b-form-select>
    </div>
    <div>
      <div class="hash">Hash: <pre>{{hash}}</pre></div>
    </div>
    <div class="user">
      <div>
        <b-form-checkbox  v-model="sendCopy">Send me a copy of the message</b-form-checkbox>
        
        <div  v-if="sendCopy">
          <b-form-input
           v-model="email" 
           placeholder="Enter email"
           type="email" id="email">  
           </b-form-input>
          <b-button v-on:click="sendMail()">
            Send
          </b-button>

          <span v-if="sent">Mail sent ✓</span>
        </div>
        
      </div>

      <b-button id="tweet" variant="primary" v-on:click="sendTweet()">Tweet it !</b-button>
      

    </div>
        
  </div>
</template>

<script>
import crypto from 'crypto';
import axios from 'axios'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'
export default {
  data() {
    return {
      msg: "Tweet Encryptor",
      tweet:'',
      composedMessage:'',
      sendCopy:false,
      email:'',
      hash:'',
      sent:false,
      algos:['MD5','RIPEMD160','SHA1','SHA224','SHA256','SHA384','SHA512'],
      algo:'SHA256'
    }
  },
  methods: {
    tweetChange(){
      if(this.tweet!==''){
        this.computeHash()
        this.composeTweet()
      } 
      else this.hash=''
    },
    computeHash(){
      if(this.tweet===''){
        this.hash=''
      }
      else {
      const secret = '';
      this.hash = crypto.createHash(this.algo, secret)
                 .update(this.tweet)
                 .digest('hex');  
      }
      
    },
    sendMail(){
      var lnk='http://curlmail.co/'+this.email+'?'
      const emailData ={
      'subject':'Tweet Encyptor',
      'content':'You sent an encrypted tweet with the message \n'+this.tweet+' \n which was encrypted using '+ this.algo+' to \n'+this.hash
      }
      lnk=lnk+this.encodeQueryData(emailData)
      axios.get(lnk)
      this.sent=true
    },
    composeTweet(){
    const link='https://twitter.com/intent/tweet?'
    
    const tweetData ={
      'text':this.hash,
      'hashtags':[this.algo,'encryptedTweet']
    }
    this.composedMessage=link+this.encodeQueryData(tweetData)
    

    },
    sendTweet() {
    window.location = this.composedMessage;
    },
    encodeQueryData(data) {
     const ret = [];
     for (let d in data)
       ret.push(encodeURIComponent(d) + '=' + encodeURIComponent(data[d]));
     return ret.join('&');
    }
  },

  watch: {
    algo: function (newAlgo){
      this.algo=newAlgo
      this.computeHash()
      this.composeTweet()
    },
    email: function(){
      this.sent=false
    }
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 20px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
.messagebox {
  max-width: 350px;
  margin-bottom: 20px;
}

#email {
  max-width: 270px;
  display: inline;
  margin-right: 15px;
  margin-bottom: 15px;
}
.algo {
  max-width: 350px;
}
.hello {
  margin: 10px;
}

.hash pre {
  display: inline;
}
</style>
