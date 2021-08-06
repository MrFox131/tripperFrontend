<template>
  <div id="root_container">
    <div class="input_container">
      <input v-model="url"><br>
      <a href="" id="check_button" v-on:click="check_button_clicked">
        <span>Button</span>
        <div class="liquid"></div>
      </a>
    </div>
    <br>
    <br>
    <div>
      Оценка по орфографии: {{SpellingErrors.result}}% <br>
      Оценка по Punicode: {{Punicode?"Положительная":"Отрицательная"}} <br>
      Оценка по перенаправлениям: {{Forwarding?"Положительная":"Отрицательная"}} <br>
      Домен похож на один из популярных: {{DomainNearPopular?"Да":"Нет"}} <br>
      Возраст домена: {{SslAndDomainAge["old"]?"Достаточен":"Недоcтаточен"}} <br>
      Есть ли SSL: {{SslAndDomainAge["result"]!="No ssl"?"Да":"Нет"}}
    </div>
  </div>
</template>

<script>

export default {
  name: 'Home',
  data: function (){
    return {
      url: "",
      socket: null,
      SslAndDomainAge: {old: false},
      SpellingErrors: {'result':0},
      Punicode: true,
      Forwarding: true,
      DomainNearPopular: null
    }
  },
  mounted() {

  },
  methods: {
    check_button_clicked(event) {
      event.preventDefault()
      console.log(this.url)
      this.socket = new WebSocket("ws://cosmos.asap-it.tech:8000/"+this.url)
      this.socket.addEventListener("open", ()=>{
        console.log("Socket connected")
        this.socket.addEventListener("message", this.onSocketMessage)
      })
    },
    onSocketMessage(event) {
      var data = JSON.parse(event.data)
      this[data.id] = data
    }
  }
}
</script>

<style>
body{
  padding: 0;
  margin: 0;
}
#root_container{
  text-align: center;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.input_container{
  text-align: center;
  align-self: center;
}
input{
  border-radius: 10px;
  padding-left: 5px ;
  padding-right: 5px;
  font-size: 1.2rem;
  width: 400px;
  height: 3rem;
  /*height: 2rem;*/
  border-style: solid;
  border-color: blueviolet;
  border-width: 2px;
  box-shadow: none!important;
}
button{
  margin-top: 10px;
}
a {
  position: relative;
  padding: 20px 50px;
  display: block;
  text-decoration: none;
  text-transform: uppercase;
  width: 100px;
  overflow: hidden;
  border-radius: 40px;
  margin: auto;
  margin-top: 10px;
}

a span {
  position: relative;
  color: #fff;
  fot-size: 20px;
  font-family: Arial;
  letter-spacing: 8px;
  z-index: 1;
}

a .liquid {
  position: absolute;
  top: -80px;
  left: 0;
  width: 200px;
  height: 200px;
  background: #4973ff;
  box-shadow: inset 0 0 50px rgba(0, 0, 0, .5);
  transition: .5s;
}

a .liquid::after,
a .liquid::before {
  content: '';
  width: 200%;
  height: 200%;
  position: absolute;
  top: 0;
  left: 50%;
  transform: translate(-50%, -75%);
  background: #000;
}

a .liquid::before {

  border-radius: 45%;
  background: rgba(20, 20, 20, 1);
  animation: animate 5s linear infinite;
}

a .liquid::after {

  border-radius: 40%;
  background: rgba(20, 20, 20, .5);
  animation: animate 10s linear infinite;
}

a:hover .liquid{
  top: -120px;
}

@keyframes animate {
  0% {
    transform: translate(-50%, -75%) rotate(0deg);
  }
  100% {
    transform: translate(-50%, -75%) rotate(360deg);
  }
}
</style>
