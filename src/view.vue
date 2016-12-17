<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 120px;

}

</style>
<template>
  <div class='loading'>
  Loading...
  </div>
</template>
<script>
/* global firebase */
import Vue from 'vue'
const queryString = require('query-string')
var config = {
  apiKey: 'AIzaSyBMrFNRf8J2m5UVgRccLMYGYa2CZdaaaao',
  authDomain: 'todos-ee737.firebaseapp.com',
  databaseURL: 'https://todos-ee737.firebaseio.com',
  storageBucket: 'todos-ee737.appspot.com',
  messagingSenderId: '909142005180'
}
var fv = firebase.initializeApp(config, 'fv')
// console.log(document.location.search)
// => '?foo=bar'

const parsed = queryString.parse(document.location.search)
console.log(parsed.imgid)
console.log('test')
// Initialize the default app

export default {
  name: 'view',
  template: '<p>Template</p>',
  data () {
    return {
      img: 'https://aframe.io/aframe/examples/boilerplate/panorama/puydesancy.jpg'
    }
  }
}

if (parsed.imgid !== undefined) {
  console.log('test')
  var pics
  var imgsrc = ''
  var starCountRef = fv.database().ref('gallery')
  console.log('gal')
  starCountRef.on('value', function (snapshot) {
    console.log('before pics')
    pics = snapshot.val()
    console.log('pics' + pics)
    var pic
    for (pic in pics) {
      console.log('pc' + pic)
      var starCountRef2 = fv.database().ref('gallery/' + pic)
      starCountRef2.on('value', function (snapshot2) {
        console.log('src2' + snapshot2.val().id.toString() + ',' + parsed.imgid.toString().trim())
        if (snapshot2.val().uuid.toString().trim() === parsed.imgid.toString().trim()) {
          console.log('new vue')
          imgsrc = snapshot2.val().imgdata
          console.log('start it')
          /* eslint-disable no-new */
          new Vue({
            el: '#app',
            template: '<a-scene><a-sky src="' + imgsrc + '" rotation="0 -130 0"></a-sky></a-scene>'
          })
        } else {
          console.log('no found')
        }
      })
    }
  })
}
</script>
