<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0px;

}
.a-enter-vr{
bottom:-120px !important;
}
</style>
<template>

  <div id="app">

  </div>
</template>
<script>
/* global firebase */

import Vue from 'vue'
const queryString = require('query-string')

// console.log(document.location.search)
// => '?foo=bar'

const parsed = queryString.parse(document.location.search)
console.log(parsed.imgid)
console.log('test')
// Initialize the default app

export default {
  name: 'app',
  template: '<p>Template</p>',
  data: function () {
    return {
      img: 'https://aframe.io/aframe/examples/boilerplate/panorama/puydesancy.jpg'
    }
  }
}
if (parsed.imgid !== undefined) {
  console.log('test')
  var pics
  var imgsrc = ''
  var starCountRef = firebase.database().ref('gallery/')
  starCountRef.on('value', function (snapshot) {
    pics = snapshot.val()

    var pic
    for (pic in pics) {
      // console.log(pic)
      var starCountRef2 = firebase.database().ref('gallery/' + pic)
      starCountRef2.on('value', function (snapshot2) {
        console.log(snapshot2.val().src)
        if (snapshot2.val().id.toString().trim() === parsed.imgid.toString().trim()) {
          imgsrc = snapshot2.val().imgdata
          /* eslint-disable no-new */
          new Vue({
            el: '#app',
            template: '<a-scene><a-sky src="' + imgsrc + '" rotation="0 -130 0"></a-sky></a-scene>'
          })
        }
      })
    }
  })
}
</script>
