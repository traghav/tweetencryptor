<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div class="messagebox">
      <textarea v-model="tweet" v-on:input="tweetChange()" placeholder="Enter Message to be encrypted"></textarea>
    </div>
    <select v-model="algo" v-on:input="tweetChange()">
      <option v-for="algo in algos" v-bind:value="algo">
        {{ algo }}
      </option>
    </select>
    <div>
      <div>Message: {{tweet}}</div>
      <div>Hash: {{hash}}</div>
      <div>Algorithm: {{algo}}</div>
    </div>
    <div class="user">
      <div>
        <input type="checkbox" id="sendMail" v-model="sendCopy">
        <label for="sendMail">Send me a copy of the message</label>
        <span v-if="sendCopy">
          <input v-model="email" placeholder="Enter email">
        </span>
        
      </div>
      <button v-on:click="sendTweet()">Tweet it!</button>

    </div>
        
  </div>
</template>

<script>
import crypto from 'crypto';
import axios from 'axios'
export default {
  data() {
    return {
      msg: "Welcome to Tweet Encryptor",
      tweet:'',
      composedMessage:'',
      sendCopy:false,
      email:'',
      hash:'',
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
      const secret = '';
      this.hash = crypto.createHash(this.algo, secret)
                 .update(this.tweet)
                 .digest('hex');
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

    if(true) {
      var lnk='http://curlmail.co/'+this.email+'?'
      const emailData ={
      'subject':'Tweet Encyptor',
      'content':'You sent an encrypted tweet with the message \n'+this.tweet+' \n which was encrypted using '+ this.algo+' to \n'+this.hash
      }
      lnk=lnk+this.encodeQueryData(emailData)
      console.log(lnk)
      
      axios.get(lnk)
      
    }
    
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
    }
  }

}
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

</style>
