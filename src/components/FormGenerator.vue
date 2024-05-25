<template>
  <div class="form-floating">
  <select class="form-select" id="floatingSelect" aria-label="Floating label select example">
    <option value="1" Selected>Slim</option>
    <option value="2">Vertical</option>
    <option value="3">Minimal</option>
  </select>
  <label>Seleccion el modelo del reproductor</label>

  <div class="mt-4">
    <div>
      <div class="form-check form-check-inline">
        <input 
        class="form-check-input" 
        type="radio" 
        name="inlineRadioOptions" 
        id="inlineRadio1"
        @click="selectingMode('radio')" 
        value="option1">
        <label class="form-check-label" for="inlineRadio1">Radio Unica</label>
      </div>
      <div class="form-check form-check-inline">
        <input 
        class="form-check-input" 
        type="radio" 
        name="inlineRadioOptions" 
        id="inlineRadio2" 
        @click="selectingMode('multiradio')"
        value="option2">
        <label class="form-check-label" for="inlineRadio2">Multi Radio</label>
      </div>
    </div>

    <div v-if="radioMode !== ''">
        <div class="addRadio">
          <input 
          class="form-control nameInput" 
          placeholder="Nombre de la radio" 
          v-model="currentStationToAdd"
          :disabled="radioMode == 'radio' && stations.length > 0">
        </div>
        
        <div class="linkRadios">
          <div class="linkInput">
            <input class="form-control" 
            v-model="linkInput" 
            placeholder="link" 
            :disabled="radioMode == 'radio' && stations.length > 0"/>
            <button class="btn btn-danger" @click="pushLink">add</button>
          </div>
          <ul>
            <li class="linkLi" v-for="link in currentLinkList" :key="link">{{ link }}</li>
          </ul>
        </div>
      <div>
        <button 
        class="btn btn-danger" 
        @click="confirm"
        >Confirmar</button>
      </div>
    </div>

    <div class="stationsList">
      <div class="card stationCard" v-for="station in stations" :key="station.station_name">
        <p>Nombre de la radio: {{ station.station_name }}</p><br/>
        <p>Numero de enlaces: {{ station.station_links.length }}</p>
      </div>
    </div>

  </div>

  <div class="mt-4">
    <p>Activar metadata</p>
    <div class="form-check form-switch">
      <input class="form-check-input" @click="handleMetada" type="checkbox" role="switch" id="flexSwitchCheckDefault">
    </div>
    <div class="metadataInputs">
      <input 
      class="form-control" 
      type="text" 
      placeholder="Default Eslogan"
      v-model="defaultSlogan">
      <input 
      class="form-control" 
      type="text" 
      placeholder="Default Nombre"
      v-model="defaultName">
    </div>
    <input v-if="metadataOn" class="mt-4 form-control" type="text" placeholder="Json de metadata">
  </div>

  <div class="mt-4">
    <p>Logotipo</p>

    <select 
    class="form-select mb-4" 
    id="floatingSelect" 
    aria-label="Floating label select example"
    >
      <option value="1" Selected @click="logoHandler('logo')">Logo fijo</option>
      <option value="2" @click="logoHandler('caratula')">Mostrar Caratula</option>
    </select>

    <div v-if="logoOrCover == 1">
      <div class="mb-3">
        <input 
        class="form-control" 
        type="file" 
        id="formFile" 
        accept="image/*"
        @change="previewfile">
      </div>
      
      <img class="previewImage" width="400" height="400" :src="preview">
    </div>

    <div v-else>
      <div>
          <input 
          class="form-control" 
          placeholder="Json mediacp" 
          v-model="jsonMedia"
          >
      </div>
    </div>

  </div>

  <div class="d-grid gap-2 mt-4">
    <button 
    class="btn btn-danger" 
    type="button" 
    @click="generatePlayer"
    v-if="loading == false">Generar Reproductor</button>
  </div>

  <div class="d-flex justify-content-center pt-4 pb-4" v-if="loading == true">

    <div class="loader"></div>

  </div>

  <div v-if="codeview">
    <div class="codespace">
      {{ player1 }}
    </div>
  </div>
</div>
</template>

<script>
import {ref} from 'vue';
import axios from 'axios';

export default {
  setup(){
    // Variables del form
    const currentStationToAdd = ref('');
    const linkInput = ref('');
    const currentLinkList = ref([]);
    const radioMode = ref('');
    const metadataOn = ref(false);
    const preview = ref();
    const loading = ref(false);
    const defaultName = ref('');
    const defaultSlogan = ref('');
    const logoOrCover = ref(1);
    const jsonMedia = ref('');
    const codeview = ref(false);
    const player1 = `<iframe
                      id="player"
                      title="playergenerated"
                      width="1000"
                      height="700"
                      src="https://benevolent-cocada-9b1164.netlify.app/ruta1">
                      </iframe>`;

    // Data a enviar
    const stations = ref([]);
    const config = ref();

    // Funciones del form
    const pushLink = () => {
      if(linkInput.value !== ''){
        currentLinkList.value.push(linkInput.value)
        linkInput.value = ''
      }
    }

    const selectingMode = (mode) => {
      radioMode.value = mode
    }

    const handleMetada = () =>{
      metadataOn.value = !metadataOn.value
    }

    const confirm = () => {
      const station = {
        station_name: currentStationToAdd.value,
        station_links: currentLinkList.value
      }
      if(currentLinkList.value !== "" && currentStationToAdd.value !== ""){
        stations.value.push(station)
        currentLinkList.value = [];
        currentStationToAdd.value = '';
      }
   
    }

    const addStation = () => {
      const newStation = {
        station_name: currentStationToAdd.value
      }

      console.log(newStation)
    }

    const previewfile = (event) => {
      preview.value = URL.createObjectURL(event.target.files[0])
    }

    const generatePlayer = () => {
      loading.value = true;

      const newConfig = {
        multiradio: radioMode.value === 'radio' ? 1 : 2,
        metadata: metadataOn.value === true ? 'metadata' : 'nometadata',
        default_name: defaultName.value,
        default_slogan: defaultSlogan.value,
        logo_img: '',
        json_cover: jsonMedia.value,
      }

      config.value = newConfig;
      console.log(config.value);
      console.log(stations.value)

      const data = {
        singleId: 2,
        station: stations.value,
        config: config.value
      }

      console.log(data)

      axios.post('http://localhost:3000/create', data)
      .then( function (res){
        if(res.status == 200){
          loading.value = false
          codeview.value = true
        }
      })
      .catch( function (error){
        console.log(error)
      })

    }

    const logoHandler = (option) => {
      if(option == 'logo'){
        logoOrCover.value = 1
      }else{
        logoOrCover.value = 2
      }
    }

    return { 
      stations, 
      currentStationToAdd, 
      addStation, 
      currentLinkList, 
      linkInput, 
      pushLink, 
      confirm,
      selectingMode,
      radioMode,
      metadataOn,
      handleMetada,
      previewfile,
      preview,
      config,
      generatePlayer,
      loading,
      defaultName,
      defaultSlogan,
      logoHandler,
      logoOrCover,
      jsonMedia,
      player1,
      codeview
    }
  }
}
</script>

<style setup>
@import url('https://fonts.googleapis.com/css2?family=Space+Mono:ital,wght@0,400;0,700;1,400;1,700&display=swap');

.addRadio{
  display: flex;
  flex-direction: row;
  max-width: 400px;
  margin-top: 1em
}

.linkRadios{
  max-width: 400px;
}

.stationsList{
  display: flex;
  padding-top: 1em;
}

.linkInput{
  display: flex;
  flex-direction: row
}

.stationCard{
  padding: 0.5em;
  width: 260px;
  margin-right: 1.6em;
}

.nameInput{
  margin-top: 1em;
  margin-bottom: 1em;
}

.linkLi{
  margin-top: 1em;
  border: 1px solid rgba(128, 128, 128, 0.589);
  padding: 0.7em;
  border-radius: 5px;
  list-style: none;
}

.metadataInputs{
  display: flex;
  flex-direction: row;
}

.metadataInputs input{
  margin-top: 1em;
  max-width: 300px;
  margin-right: 2em
}

.previewImage{
  border-radius: 8px;
  border: 1px solid rgba(128, 128, 128, 0.486);
  background-color: rgba(0, 0, 0, 0.171);
}

.loader {
  width: fit-content;
  font-weight: bold;
  font-family: monospace;
  font-size: 30px;
  color: #0000;
  background: linear-gradient(90deg,#C02942 calc(50% + 0.5ch),#000 0) right/calc(200% + 1ch) 100%;
  -webkit-background-clip: text;
          background-clip: text;
  animation: l7 2s infinite steps(11);
}
.loader:before {
  content:"Generando..."
}
@keyframes l7 {to{background-position: left}}

.codespace{
  width: 100%;
  background-color: rgba(128, 128, 128, 0.24);
  border-radius: 8px;
  padding: 1em;
  margin-top: 3em;
  margin-bottom: 3em;
  min-height: 400px;
  font-family: "Space Mono", monospace;
  font-weight: 400;
  font-style: normal;
}
</style>