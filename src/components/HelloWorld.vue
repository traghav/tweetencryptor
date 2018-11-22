<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <div class="messagebox">
      <textarea v-model="tweet" v-on:input="tweetChange()"></textarea>
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
    <a :href="composedMessage">Tweet it!</a>
  </div>
</template>

<script>
import crypto from 'crypto';
export default {
  data() {
    return {
      msg: "Welcome to Tweet Encryptor",
      tweet:'',
      preMessage:'https://twitter.com/intent/tweet?text=',
      composedMessage:'',
      hash:'',
      algos:['MD5','RIPEMD160','SHA1','SHA224','SHA256','SHA384','SHA512'],
      algo:'SHA256'
    }
  },
  methods: {
    tweetChange(){
      if(this.tweet!==''){
        this.computeHash()
        this.composedMessage=this.preMessage+this.hash
      } 
      else this.hash
    },
    computeHash(){
      const secret = '';
      this.hash = crypto.createHash(this.algo, secret)
                 .update(this.tweet)
                 .digest('hex');
    }
  },
  watch: {
    algo: function (newAlgo){
      this.algo=newAlgo
      this.computeHash()
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
a {
  color: #42b983;
}
</style>
