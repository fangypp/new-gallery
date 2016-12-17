<template>
  <div id="app">
    <input
        v-model.trim="src"
        placeholder="คำอธิบาย"
        style="display:none"
        >

     <textarea v-model="imgdata" placeholder="add multiple lines" style='display:none'></textarea>
     <center>
   <table><tr>
   <td style='text-align:right'>ชื่อภาพ :</td><td>
      <input
        v-model="message"
        placeholder="message"
      >
    </td></tr>
    <tr>
    <td style='text-align:right'>ผู้ส่ง :</td><td>
    <input
        v-model="sender"
        placeholder="ชื่อผู้ส่ง"
      >
    </td>
    </tr>
    <tr>
    <td style='text-align:right'>รูปภาพ :</td><td>
     <img :src="image" style='max-height:150px' id='cover' name='cover' /><br />
    <input type="file" name="p" id="p" v-on:change="onFileChange">

    </td>
    </tr>
    <tr>
    <td></td>
    <td> <button type="button" @click="addGallery()" class="btn btn-primary">ส่ง</button>
    </td>
    </tr>
    </table>
    </center>
    <center>
<div class='table'>
    <div class='row' v-for="todo in todos">
    <img v-bind:src="todo.imgdata" height='100px' />
    <br />
    {{todo.message}}
    <br />
    ส่งโดย {{todo.sender}}
    <br />
    <a href='#' @click="navigate(todo)">View </a>

	<button @click="removeTodo(todo)">X</button>
    <br />
    </div>
</div>
</center>
  </div>
</template>

<script>
/* global FileReader,Image */
// var db = firebase.initializeApp({
//   apiKey: 'AIzaSyBMrFNRf8J2m5UVgRccLMYGYa2CZdaaaao',
//   databaseURL: 'https://todos-ee737.firebaseio.com'
// }).database()
// var todosRef = db.ref('gallery')
import firebase from 'firebase'
var config = {
  apiKey: 'AIzaSyBMrFNRf8J2m5UVgRccLMYGYa2CZdaaaao',
  authDomain: 'todos-ee737.firebaseapp.com',
  databaseURL: 'https://todos-ee737.firebaseio.com',
  storageBucket: 'todos-ee737.appspot.com',
  messagingSenderId: '909142005180'
}
firebase.initializeApp(config)
var Todos = firebase.database().ref('gallery')
// var storage = firebase.storage()
// var storageRef = storage.ref('file')

export default {
  name: 'app',
  data () {
    return {
      message: '',
      sender: '',
      image: '',
      src: '',
      imgdata: '',
      file: '',
      todos: []
    }
  },
  mounted () {
    console.log('mm')
    console.log('Ready')
    var vm = this
    Todos.on('child_added', function (data) {
      console.log(data.val())
      var item = data.val()
      item.id = data.key
      vm.todos.push(item)
    })
    Todos.on('child_removed', function (data) {
      var id = data.key
      var index = vm.todos.findIndex(todo => todo.id === id)
      vm.todos.splice(index, 1)
    })
  },
  methods: {
    onFileChange (e) {
      var files = e.target.files || e.dataTransfer.files
      console.log('Filename ' + files[0].name)
      if (!files.length) {
        return false
      }
      this.createImage(files[0])
      this.src = files[0].name
      // uploadImageGallery();
      // this.file = files[0]
    },
    addGallery: function () {
      console.log(this.message)
      if (this.message) {
        Todos.push({
          message: this.message,
          sender: this.sender,
          src: this.src,
          imgdata: this.imgdata,
          id: Math.random()
        })
        this.message = ''
        this.sender = ''
        this.src = ''
        this.image = ''
        this.imgdata = ''
        this.cover = ''
        this.id = ''
      }
      // var vm = this
      // console.log('Uploading')
      // storageRef.child(Date.now() + '/' + vm.file.name).put(vm.file).then(function (snapshot) {
      //   console.log(snapshot.downloadURL)
      //   console.log(vm.message)
      //   vm.src = snapshot.downloadURL
      //   if (vm.message) {
      //     Todos.push({
      //       message: vm.message,
      //       sender: vm.sender,
      //       src: vm.src,
      //       imgdata: vm.src,
      //       id: Math.random()
      //     })
      //     vm.message = ''
      //     vm.sender = ''
      //     vm.src = ''
      //     vm.image = ''
      //     vm.imgdata = ''
      //     vm.cover = ''
      //     vm.id = ''
      //   }
      // })
    },
    createImage (file) {
      var image = new Image()
      var reader = new FileReader()
      var vm = this

      reader.onload = (e) => {
        console.log('Target ' + e.target)
        this.imgdata = e.target.result
        vm.image = e.target.result
        console.log(image)
      }
      reader.readAsDataURL(file)
    },
    // updateTodoText: function (todo, newText) {
    //   todosRef.child(todo['.key']).child('text').set(newText)
    // },
    // removeTodo: function (todo) {
    //   todosRef.child(todo['.key']).remove()
    // },
    navigate: function (todo) {
      // window.open('/view?imgid=' + todo.id)
      console.log(todo.id)
      // window.location.replace('/view?imgid=' + todo.id)
      window.location.replace('/view?imgid=' + todo.id)
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
