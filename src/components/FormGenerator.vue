<template>
  <div class="form-floating">
    <div class="step1" v-if="step == 0">
        <h3>Elige un modo para tu reproductor</h3>
        <div class="options">
            <div class="item" @click="changeMode('slim')" :style="[ mode == 'slim' ? {'border-color': 'rgba(255, 0, 0, 0.658)'} : {'border-color': 'rgba(128, 128, 128, 0.158)'}]" >
                <img :src="slim" width="200" height="120"/>
                <span>Slim</span>
            </div>
            <div class="item" @click="changeMode('vertical')" :style="[ mode == 'vertical' ? {'border-color': 'rgba(255, 0, 0, 0.658)'} : {'border-color': 'rgba(128, 128, 128, 0.158)'}]">
                <img :src="vertical" width="200" height="120"/>
                <span>Vertical</span>
            </div>
            <div class="item" @click="changeMode('minimal')" :style="[ mode == 'minimal' ? {'border-color': 'rgba(255, 0, 0, 0.658)'} : {'border-color': 'rgba(128, 128, 128, 0.158)'}]">
                <img :src="minimal" width="200" height="120"/>
                <span>Minimal</span>
            </div>
        </div>

        <button class="btn btn-danger" @click="handleStep">Confirmar</button>
    </div>
    <div v-if="step == 1">

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
          :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false">
        </div>

        <div class="metadataInputs">
          <input 
          class="form-control" 
          type="text" 
          placeholder="Default Eslogan"
          :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"
          v-model="defaultSlogan">
        </div>
        
        <div class="linkRadios">
          <div class="linkInput">
            <input class="form-control" 
            v-model="linkInput" 
            placeholder="link" 
            :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"/>
            <button class="btn btn-danger" @click="pushLink">add</button>
          </div>
          <ul>
            <li 
            class="linkLi" 
            v-for="(link, index) in currentLinkList" 
            :key="index"
            >{{ link }}
            <div class="LiPanel">
              <div class="upDown">
                <UpIcon @click="changeLinkPosition('up', link, index)"></UpIcon>
              </div>
              <TrashIcon 
              class="trashIcon"
              @click="deleteLink(link)"></TrashIcon>
            </div>
          </li>
          </ul>
        </div>
      
    </div>

  </div>

  <div class="RadioData" v-show="radioMode !== ''">
    <div class="mt-4">
    <p>Activar metadata</p>
    <div class="form-check form-switch">
      <input class="form-check-input" @click="handleMetada" type="checkbox" role="switch" id="flexSwitchCheckDefault">
    </div>
    <input 
    v-if="metadataOn" 
    class="mt-4 form-control" 
    type="text" 
    placeholder="Json de metadata"
    v-model="metadataLink">
  </div>

  <div class="mt-4">

    <div v-if="metadataOn == false">
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

  </div>

  <div v-if="editionMode === false">
    <button 
    class="btn btn-danger" 
    @click="confirm"
    >Confirmar</button>
  </div>

  <div v-else class="editionButtons">
    <button 
    class="btn btn-primary" 
    @click="confirmEdition(oldStationName)"
    >Guardar cambios</button>

    <button 
    class="btn btn-danger" 
    @click="cancelEdition"
    >Cancelar</button>
  </div>

  <div class="stationsList" v-show="editionMode === false">
      <div class="card stationCard" v-for="(station, index) in stations" :key="station.station_name">
        <div class="buttonsStations">
          <TrashIcon @click="deleteStation(station.station_name)"></TrashIcon>
          <EditIcon @click="editStation(station.station_name)"></EditIcon>
          <LeftIcon @click="changeRadioPosition(index)"></LeftIcon>
        </div>
        <p>Nombre de la radio: {{ station.station_name }}</p><br/>
        <p>Numero de enlaces: {{ station.station_links.length }}</p><br/>
        <p>Metadata: {{ metadataOn === true ? 'Si' : 'No' }}</p>
      </div>
  </div>

  <div class="d-grid gap-2 mt-4" v-if="stations.length > 0 && editionMode === false">
    <button 
    class="btn btn-danger" 
    type="button" 
    @click="generatePlayer"
    v-if="loading == false">Generar Reproductor</button>
  </div>
  </div>

  <div class="d-flex justify-content-center pt-4 pb-4" v-if="loading == true">

    <div class="loader"></div>

  </div>

  <div v-if="codeview && typeOfPlayer == 'slim'">
    <div class="codespace">
      &lt;iframe
      id=&quot;player&quot;
      title=&quot;playergenerated&quot;
      width=&quot;700&quot;
      height=&quot;1000&quot;
      src=&quot;https://benevolent-cocada-9b1164.netlify.app/slim/{{paramToPlayer}}&quot;&gt;
      &lt;/iframe&gt;
    </div>
  </div>

  <div v-else-if="codeview && typeOfPlayer == 'vertical'">
    <div class="codespace">
      &lt;iframe
      id=&quot;player&quot;
      title=&quot;playergenerated&quot;
      width=&quot;1000&quot;
      height=&quot;700&quot;
      src=&quot;https://benevolent-cocada-9b1164.netlify.app/vertical/{{paramToPlayer}}&quot;&gt;
      &lt;/iframe&gt;
    </div>
  </div>

  <div v-else-if="codeview && typeOfPlayer == 'minimal'">
    <div class="codespace">
      &lt;iframe
      id=&quot;player&quot;
      title=&quot;playergenerated&quot;
      width=&quot;800&quot;
      height=&quot;200&quot;
      src=&quot;https://benevolent-cocada-9b1164.netlify.app/minimal/{{paramToPlayer}}&quot;&gt;
      &lt;/iframe&gt;
    </div>
  </div>
    </div>
  </div>
</template>

<script>
import {ref} from 'vue';
import axios from 'axios';
import EditIcon from './icons/EditIcon.vue'
import TrashIcon from './icons/TrashIcon.vue'
import UpIcon from './icons/UpIcon.vue'
import LeftIcon from './icons/LeftIcon.vue'

export default {
  components:{
    EditIcon,
    TrashIcon,
    UpIcon,
    LeftIcon
  },
  setup(){
    // Variables del form
    const currentStationToAdd = ref('');
    const linkInput = ref('');
    const currentLinkList = ref([]);
    const radioMode = ref('');
    const metadataOn = ref(false);
    const preview = ref();
    const loading = ref(false);
    const defaultSlogan = ref(''); // Eslogan por default
    const logoOrCover = ref(1);
    const jsonMedia = ref(''); // Json de la metadata
    const typeOfPlayer = ref('');
    const codeview = ref(false);
    const paramToPlayer = ref('');
    const metadataLink = ref('');
    const editionMode = ref(false);
    const oldStationName = ref('');
    const step = ref(0)
    const mode = ref();
    const slim = ref(require('../assets/slim.png'));
    const vertical = ref(require('../assets/vertical.png'));
    const minimal = ref(require('../assets/minimal.png'));

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

    const changeLinkPosition = (action ,actualLink, currentIndex) => {
      const beforeValue = currentIndex - 1
      let temp = currentLinkList.value[currentIndex]
      let tempBefore = currentLinkList.value[beforeValue]
      if(temp && tempBefore){
        currentLinkList.value[beforeValue] = temp
        currentLinkList.value[currentIndex] = tempBefore
      }
    }

    // Mover prioridad de Radio
    const changeRadioPosition = (currentIndex) => {
      console.log(currentIndex)
      const beforeValue = currentIndex - 1;
      const temp = stations.value[currentIndex];
      const tempBefore = stations.value[beforeValue];
      if(temp && tempBefore){
        stations.value[beforeValue] = temp;
        stations.value[currentIndex] = tempBefore;
      }
    }

    const changeMode = (val) => {
            mode.value = val
            typeOfPlayer.value = val
    }

    const handleStep = () => {
      step.value = 1
    }

    const deleteLink = (link) => {
      let newLinks = []
      currentLinkList.value.forEach((val) => {
        if(val !== link){
          newLinks.push(val)
        }
      })
      currentLinkList.value = newLinks
    }

    const changeTypeOfPlayer = (type) => {
      typeOfPlayer.value = type
    }

    const selectingMode = (mode) => {
      radioMode.value = mode
    }

    const handleMetada = () =>{
      metadataOn.value = !metadataOn.value
    }

    const deleteStation = (stationName) => {
      let newStations = []
      stations.value.forEach(val => {
        if(val.station_name !== stationName){
          newStations.push(val)
        }
      })

      stations.value = newStations
    }

    const editStation = (stationName) => {
      editionMode.value = true
      const result = stations.value.find(val => val.station_name === stationName);
      currentLinkList.value = result.station_links
      defaultSlogan.value = result.defaultSlogan
      currentStationToAdd.value = result.station_name
      metadataLink.value = result.metadata
      oldStationName.value = stationName
    }

    const confirmEdition = (stationName) => {
      let stationValues = []
      stations.value.forEach((val)=> {
        if(val.station_name == stationName){
          const station = {
            station_name: currentStationToAdd.value,
            station_links: currentLinkList.value,
            defaultSlogan: defaultSlogan.value,
            metadata: metadataLink.value
          }
          stationValues.push(station)
        }else{
          stationValues.push(val)
        }
      })
      stations.value = stationValues
      editionMode.value = false
      cancelEdition()
    }

    const cancelEdition = () => {
      editionMode.value = false
      currentLinkList.value = ''
      defaultSlogan.value = ''
      currentStationToAdd.value = ''
      metadataLink.value = ''
      oldStationName.value = ''
    }

    const confirm = () => {
      const station = {
        station_name: currentStationToAdd.value,
        station_links: currentLinkList.value,
        defaultSlogan: defaultSlogan.value,
        metadata: metadataLink.value
      }
      if(currentLinkList.value !== "" && currentStationToAdd.value !== ""){
        stations.value.push(station)
        currentLinkList.value = [];
        currentStationToAdd.value = '';
      }

      // Reiniciar datos en el formulario
      defaultSlogan.value = ''
      jsonMedia.value = ''
      metadataLink.value = ''
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

      axios.post('https://player-radio.inovanex.com/create', data)
      .then( function (res){
        if(res.status == 200){
          const resName = res.data.name.replace(/\s/g, '')
          paramToPlayer.value = resName
          loading.value = false
          codeview.value = true
          console.log(typeOfPlayer.value)
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
      defaultSlogan,
      logoHandler,
      logoOrCover,
      jsonMedia,
      codeview,
      typeOfPlayer,
      changeTypeOfPlayer,
      paramToPlayer,
      step,
      mode,
      slim,
      vertical,
      minimal,
      changeMode,
      handleStep,
      deleteStation,
      metadataLink,
      editStation,
      editionMode,
      confirmEdition,
      cancelEdition,
      oldStationName,
      deleteLink,
      changeLinkPosition,
      changeRadioPosition
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

.upDown{
  display: flex;
  flex-direction: column-reverse;
  justify-content: center;
  align-items: center;
}

.trashIcon {
  margin-left: 15px;
}

.LiPanel{
  display: flex;
  align-items: center;
}

.stationsList{
  display: flex;
  padding-top: 1em;
  flex-wrap: wrap
}

.linkInput{
  display: flex;
  flex-direction: row
}

.stationCard{
  padding: 1em;
  width: 260px;
  margin-right: 1.6em;
  margin-top: 0.6em;
}

.nameInput{
  margin-top: 1em;
  margin-bottom: 1em;
}

ul{
  padding: 0px;
}

.linkLi{
  margin-top: 1em;
  border: 1px solid rgba(128, 128, 128, 0.589);
  padding: 0.7em;
  border-radius: 5px;
  list-style: none;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.metadataInputs{
  display: flex;
  flex-direction: row;
}

.metadataInputs input{
  max-width: 400px;
  margin-bottom: 1em;
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

.options {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
}

.step1 {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
    min-height: 60vh;
}

.item {
    padding: 1em;
    border-radius: 8px;
    border: 3px solid rgba(128, 128, 128, 0.158);
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin: 1em;
}

.item:hover {
    border: 3px solid rgba(255, 0, 0, 0.658);
    cursor: pointer;
}

.item img {
    border-radius: 8px;
    margin-bottom: 1em;
}

.buttonsStations{
  display: flex;
  flex-direction: row-reverse;
  margin-bottom: 1em;
}

.buttonsStations svg {
  margin-left: 1em;
  cursor: pointer
}

.editionButtons button{
  margin-left: 1em;
}

svg{
  cursor: pointer;
}
</style>