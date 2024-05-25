<template>
    <div class="bg"></div>
    <div class="bg bg2"></div>
    <div class="bg bg3"></div>
    <div class="content">
        <div class="form">
            <FormGenerator />
        </div>
    </div>
</template>

<script>
import FormGenerator from './FormGenerator.vue'

export default {
  name: 'App',
  components: {
    FormGenerator
  }
}
</script>

<style>
html {
  height:100%;
}

body {
  margin:0;
}

.bg {
  animation:slide 3s ease-in-out infinite alternate;
  background-image: linear-gradient(-60deg, #ce0011 50%, #000000 50%);
  bottom:0;
  left:-50%;
  opacity:.5;
  position:fixed;
  right:-50%;
  top:0;
  z-index:-1;
}

.bg2 {
  animation-direction:alternate-reverse;
  animation-duration:4s;
}

.bg3 {
  animation-duration:5s;
}

.content {
    display: flex;
    justify-content: center;
    padding: 3em;
}

.form{
    width: 990px;
    background-color: #faede3;
    border-radius: 5px;
    padding: 1em;
}

h1 {
  font-family:monospace;
}

@keyframes slide {
  0% {
    transform:translateX(-25%);
  }
  100% {
    transform:translateX(25%);
  }
}
</style>