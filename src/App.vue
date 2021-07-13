<template>
  <div id="app">
    <header class="navbar">
      <section class="navbar-section">
        <a href="/" class="navbar-brand mr-2 text"><img class="img-responsive img-logo" alt="Vue logo" src="./assets/clima.png"></a> 
        
        <span class="text"> CLIMA </span> 
        <!-- <a href="/List" class="text">Listar Climas</a> -->
      </section>
      <section class="navbar-section">
        <div class="">
          <div class="input-group">
              <input type="text" v-model="city" class="form-input" placeholder="Informe a cidade"/>
              <button class="btn btn-primary input-group-btn" @click="send()">Enviar</button>
            </div>
        </div>
        
      </section>
    </header>
    <div class="container grid-lg">
      <div class="columns col-gapless">
        <div class="column col-6">  
          
          <div class="control" v-if="info">
            <img class="icon" :src="require(`../public/conditions/${prev.icon}.svg`)" alt="daily item" />
            <!-- {{prev.icon}} -->
            <p class="temp">{{round(prev.temp)}}º</p>
            <p class="city">{{prev.name}}</p>
            <p class="min">Min: {{round(prev.min)}}º</p>
            <p class="max">Max: {{round(prev.max)}}º</p>
            <p class="condition">{{capitalize(prev.description)}}</p>
            <div class="rate">
              <star-rating :star-size="20" @rating-selected ="setRating"></star-rating>
            </div>
            <button class="btn btn-success link-group" @click="save(prev)">Avaliar</button>
          </div>
          <div class="control" v-if="!info">
          <h2 class="title">Informe a cidade.</h2> 
          </div>
        </div>
        <div class="col-6 border-left"  v-if="predictions">
          <div class="divider">
            <h2 class="pred-title">
              Previsões Salvas
            </h2>
          </div>
        <div class="col-12 mb-12" v-for="item of predictions" v-bind:key="item">
          
          <div  class="pred-control">
          
            <img class="icon" :src="require(`../public/conditions/${item.icon}.svg`)" alt="daily item" />
            <!-- {{item.icon}} -->
            <p class="temp">{{round(item.temp)}}º</p>
            <p class="city">{{item.name}}</p>
            <p class="min">Min: {{round(item.min)}}º</p>
            <p class="max">Max: {{round(item.max)}}º</p>
            <p class="condition">{{capitalize(item.description)}}</p>
            <div class="rate">
              <star-rating :star-size="20" :rating="item.rating" :read-only="true"></star-rating>
            
          
              
          
            
            </div>
            
          </div>
          
          </div>
        
          
        </div>
      </div>
    </div>
    
    
    
  </div>
</template>

<script>
import StarRating from 'vue-star-rating'

import axios from 'axios'
export default {
  name: 'App',
  components: {
    StarRating
  },
  props: {
    star: {
      type: Number,
      default: 1
    },
    ratingdescription: {
      type: Array,
      default() {
        return [
          {
            text: "Muito ruim",
            class: "star-poor"
          },
          {
            text: "Ruim",
            class: "star-belowAverage"
          },
          {
            text: "Médio",
            class: "star-average"
          },
          {
            text: "Bom",
            class: "star-good"
          },
          {
            text: "Excelente",
            class: "star-excellent"
          }
        ];
      }
    },
    hasresults: {
      type: Boolean,
      default: true
    },
    hasdescription: {
      type: Boolean,
      default: true
    },
    starsize: {
      type: String,
      default: "lg" //xs/6x
    },
    maxstars: {
      type: Number,
      default: 5
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  //components: {Navbar},
  data () {
    return {
      predictions: [],
      prev:{
        name: null,
        temp: null,
        min: null,
        max: null,
        icon: null,
        condition: null,
        description: null,
        rating: 0
      },
      city:null,
      info: null,
      key: '205c359dced04080bf693ee22b0f5ff7',
      
    }
  },
  created () {
     this.predictions = JSON.parse(localStorage.getItem('predictions'))
  },
 
  methods: {
    async send(){
      axios
      .get('http://api.openweathermap.org/data/2.5/weather?q='+this.city+'&units=metric&lang=pt_br&appid='+this.key)
      .then(response => {
        this.info = response.data
        console.log('Response',this.info)
        this.prev.name = this.info.name
        this.prev.temp = this.info.main.temp
        this.prev.min = this.info.main.temp_min
        this.prev.max = this.info.main.temp_max
        this.prev.icon = this.info.weather[0].icon
        this.prev.description = this.info.weather[0].description
        console.log('Response',this.prev)
      })

      
      //alert(this.city);
      // let config = {
      //   headers:{
      //     'Accept': 'application/json'
      //   }
      // }
      // const res = await axios.get('http://api.openweathermap.org/data/2.5/weather?q='+this.city+'&appid='+this.key,config)
      // this.info = res.data
      // console.log('Response',this.info.weather)
    },
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    },
    setRating: function(rating){
      this.prev.rating = rating;
      console.log('Avaliação',this.prev.rating)
    },
    round: function(value){
      if(!value) return ''
      return value = value.toFixed()
    },
    save(prev){
      let pred = localStorage.getItem('predictions')
      if(pred){
        pred = JSON.parse(pred)
        pred.push(prev)
      }else{
        pred = [prev]
      }
      this.predictions = pred
      localStorage.setItem('predictions',JSON.stringify(pred))
    }
    
  }
  
}
</script>
<style scoped>
  .img-logo{
    max-width: 50px;
    margin: 0 auto;
  }
  .icon{
    color:white;
    max-width: 50px;
    margin: 0 auto;
    font-size: 30px;
    float: right;
  }
  .temp{
    color: #fff;
    font-size: 42px;
    margin-top: 0%;
  }
  .city{
    color: #fff;
    font-size: 22px;
  }
  .min{
    float: right;
    color: #fff;
    font-size: 12px;
    margin-left: 45px;
    margin-top: -15%;
    position: relative;
  }
  .max{
    float: right;
    color: #fff;
    font-size: 12px;
    margin-left: 45px;
    margin-top: -10%;
    position: relative;
  }
  .condition{
    margin-top: -3%;
    float: left;
    color: #fff;
    font-size: 12px;
  }
  .control{
    padding: 10px;
    border-radius: 10px;
    margin-top: 10%;
    margin-left: 5%;
    background-image: linear-gradient(#0765a3,black);
    height: auto;
  }
  .pred-control{
    padding: 10px;
    border-radius: 10px;
    margin-top: 8%;
    margin-left: 5%;
    background-image: linear-gradient(#0765a3,black);
    border: 1px solid #ccc;
  }
  .input-group{
    margin-top: 0%;
    margin-right: 5%;
    color:#fff;
  }
  .link-group{
    margin-top: 0%;
    margin-left: 80%;
    color:#fff;
    border-radius: 10%;
  }
  .navbar-section{
    background-color: #0765a3;
    
    padding: 1%;
  }
.divider{
    color: #ccc;
    font-size: 18px;
    padding: 1px;
    border-top: 0;
    margin-top: 2%;
    margin-left: 5%;
  }
  .text{
    color:#fff;
    font-size: 22px;
    padding: 2px;
  }
  .rate{
    float: right;
    margin-top: -5%;
    color: #fff;
    margin-right: 0%;
  }
  .title{
    color: #fff;
  }
</style>
