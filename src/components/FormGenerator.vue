<template>
  <div class="form-floating">
    <div class="step1" v-if="editionLinkMode === false">

      <span> Modelo del reproductor: </span>
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

    </div>
    <div>

      <div v-if="editionLinkMode === true">
        <div class="linkInput">

          <span>Editar enlace</span>
          
          <input class="form-control dataInputs" 
          v-model="linkEditInput" 
          placeholder="Enlace de audio" 
          />
          
          <input class="form-control dataInputs" 
          v-model="metadataEditInput" 
          placeholder="Enlace de Metadata" 
          />
          
          <input class="form-control dataInputs" 
          v-model="programmingEditInput" 
          placeholder="Enlace de Programacion" 
          />

          <div class="d-flex mt-4">
            <button class="btn btn-danger me-4" @click="confirmEditLink(oldLinkId)">Confirmar</button>
            <button class="btn btn-warning" @click="cancelEditLink">Cancelar</button>
          </div>
        </div>
      </div>

  <div class="mt-4" v-if="editionLinkMode === false">
    <div>
      <div class="form-check form-check-inline">
        <input 
        class="form-check-input" 
        type="radio" 
        name="inlineRadioOptions" 
        id="inlineRadio1"
        @click="selectingMode('radio')"
        required
        value="option1">
        <label class="form-check-label" for="inlineRadio1">Radio Unica</label>
      </div>
      <div class="form-check form-check-inline">
        <input 
        class="form-check-input" 
        type="radio" 
        name="inlineRadioOptions" 
        id="inlineRadio2"
        required
        @click="selectingMode('multiradio')"
        value="option2">
        <label class="form-check-label" for="inlineRadio2">Multi Radio</label>
      </div>
    </div>

    <div>
        <div class="addRadio">
          <input 
          class="form-control nameInput" 
          required
          placeholder="Nombre de la radio" 
          v-model="currentStationToAdd"
          :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false">
        </div>

        <div class="metadataInputs">
          <input 
          class="form-control" 
          type="text" 
          required
          placeholder="Default Eslogan"
          :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"
          v-model="defaultSlogan">
        </div>
        
        <div class="linkRadios">
          <div class="linkInput">

            <input class="form-control dataInputs" 
            v-model="linkInput" 
            placeholder="Enlace de audio" 
            :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"/>

            <input class="form-control dataInputs" 
            v-model="metadataInput" 
            placeholder="Enlace de Metadata" 
            :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"/>

            <input class="form-control dataInputs" 
            v-model="programmingInput" 
            placeholder="Enlace de Programacion" 
            :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"/>

            <button class="btn btn-danger" @click="pushLink">Agregar a la lista</button>
          </div>
          <ul>
            <li 
            class="linkLi" 
            v-for="(link, index) in currentLinkList" 
            :key="index"
            >
            <div class="liData">
              <span>Enlace: {{ link.link }}</span>
              <span>Metadata: {{ link.metadata }}</span>
              <span>Programacion: {{ link.programming }}</span>
            </div>
            <div class="LiPanel">
              <div class="upDown">
                <UpIcon 
                @click="changeLinkPosition('up', link.link, index)"></UpIcon>
              </div>
              <EditIcon @click="editLink(link.link)"></EditIcon>
              <TrashIcon 
              class="trashIcon"
              @click="deleteLink(link.link)"></TrashIcon>
            </div>
          </li>
          </ul>
        </div>
      
    </div>

  <div class="mt-4">

    <span class="mb-3">Caratula por defecto:</span>

    <div class="mt-3">
      <div class="mb-3">
        <input
        class="form-control mt-3" 
        type="file" 
        id="formFile" 
        accept="image/*"
        @change="previewfile">
      </div>
      
      <img class="previewImage mt-3" v-show="preview" width="230" height="230" :src="preview">
    </div>

  </div>

  <div class="mt-4">

    <span>Color tema y color de texto</span>
    <div class="colorPickers d-flex mt-4">
      <input 
      class="colorPicker me-4"
      :style="{borderColor: colorTheme}" 
      v-model="colorTheme" 
      type="text" 
      data-coloris>
      <input 
      class="colorPicker"
      :style="{borderColor: colorTheme2}" 
      v-model="colorTheme2" 
      type="text" 
      data-coloris>  
    </div>
  </div>

  <div class="mt-4">
    <div class="form-floating">
      <select class="form-select" id="floatingSelect" aria-label="Floating label select example">
        <option value="Roboto Mono" class="robotoFont">Roboto Mono</option>
        <option value="Montserrat" class="monserrat">Montserrat</option>
        <option value="Raleway" class="Ralway">Raleway</option>
        <option value="Playwrite DE Grund" class="playwrite">Play Write</option>
        <option value="Playwrite DE Grund">Poppins</option>
        <option value="Lato" class="lato">Lato</option>
        <option value="Arimo" class="Arimo">Helvetica</option>
      </select>
      <label for="floatingSelect">Selecciona una fuente</label>
    </div>
  </div>

  <div v-if="editionMode === false" class="mt-5">
    <button 
    class="btn btn-danger" 
    @click="confirm"
    >Confirmar</button>
  </div>

  <div v-else-if="editionMode == true && radioMode !== ''" class="editionButtons">
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
    LeftIcon,
    },
  setup(){
    // Variables del form
    const currentStationToAdd = ref('');
    const linkInput = ref('');
    const currentLinkList = ref([]);
    const radioMode = ref('');
    const metadataOn = ref(false);
    const metadataInput = ref('');
    const programmingInput = ref('');
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
    const mode = ref();
    const slim = ref(require('../assets/slim.png'));
    const vertical = ref(require('../assets/vertical.png'));
    const minimal = ref(require('../assets/minimal.png'));
    const colorTheme = ref('#cd2327');
    const colorTheme2 = ref('#e2e2e2')
    const defaultImage = ref('');

    //Inputs de edicion
    const editionLinkMode = ref(false)
    const linkEditInput = ref('');
    const metadataEditInput = ref('');
    const programmingEditInput = ref('');
    const oldLinkId = ref('')

    // Data a enviar
    const stations = ref([]);
    const config = ref();

    // Funciones del form
    const pushLink = () => {
      if(linkInput.value !== ''){

        const radio = {
          link: linkInput.value,
          metadata: metadataInput.value,
          programming: programmingInput.value
        }

        currentLinkList.value.push(radio)
        linkInput.value = '';
        metadataInput.value = '';
        programmingInput.value = '';

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

    const deleteLink = (link) => {
      let newLinks = []
      console.log(link)
      currentLinkList.value.forEach((val) => {
        if(val.link !== link){
          newLinks.push(val)
        }
      })
      currentLinkList.value = newLinks
      console.log(newLinks)
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

    const editLink = (link) => {
      editionLinkMode.value = true
      const result = currentLinkList.value.find(val => val.link === link);
      console.log(result)
      oldLinkId.value = result.link
      linkEditInput.value = result.link
      programmingEditInput.value = result.programming
      metadataEditInput.value = result.metadata
    }

    const cancelEditLink = () => {
      editionLinkMode.value = false
      linkEditInput.value = ''
      programmingEditInput.value = ''
      metadataEditInput.value = ''
    }

    const editStation = (stationName) => {
      editionMode.value = true
      const result = stations.value.find(val => val.station_name === stationName);
      console.log(result)
      currentLinkList.value = result.station_links
      defaultSlogan.value = result.defaultSlogan
      currentStationToAdd.value = result.station_name
      metadataLink.value = result.metadata
      oldStationName.value = stationName
    }

    const confirmEditLink = (link) => {
      let linkValues = []
      currentLinkList.value.forEach(val => {
        if(val.link == link){
          const linkData = {
            link: linkEditInput.value,
            metadata: metadataEditInput.value,
            programming: programmingEditInput.value,
          }
          linkValues.push(linkData)
        }else{
          linkValues.push(val)
        }
      })
      console.log(linkValues)
      currentLinkList.value = linkValues
      editionLinkMode.value = false
      cancelEditLink()
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
      const form = new FormData();
      form.append('defaultImage', event.target.files[0])
      defaultImage.value = form
      preview.value = URL.createObjectURL(event.target.files[0])
    }

    const generatePlayer = () => {
      loading.value = true;

      const newConfig = {
        multiradio: radioMode.value === 'radio' ? 1 : 2,
        metadata: metadataOn.value === true ? 'metadata' : 'nometadata',
        default_slogan: defaultSlogan.value,
        logo_img: defaultImage.value,
        json_cover: jsonMedia.value,
        color: colorTheme.value
      }

      config.value = newConfig;

      const data = {
        station: stations.value,
        config: config.value
      }

      console.log(data)

      const url = 'https://player-radio-backend.inovanex.com/create';
      axios.post(url, data)
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
      mode,
      slim,
      vertical,
      minimal,
      changeMode,
      deleteStation,
      metadataLink,
      editStation,
      editionMode,
      confirmEdition,
      cancelEdition,
      oldStationName,
      deleteLink,
      changeLinkPosition,
      changeRadioPosition,
      metadataInput,
      programmingInput,
      colorTheme,
      colorTheme2,
      programmingEditInput,
      metadataEditInput,
      linkEditInput,
      editLink,
      editionLinkMode,
      cancelEditLink,
      confirmEditLink,
      oldLinkId
    }
  }
}
</script>

<style setup>
@import url('https://fonts.googleapis.com/css2?family=Space+Mono:ital,wght@0,400;0,700;1,400;1,700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono:ital,wght@0,100..700;1,100..700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&family=Roboto+Mono:ital,wght@0,100..700;1,100..700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&family=Raleway:ital,wght@0,100..900;1,100..900&family=Roboto+Mono:ital,wght@0,100..700;1,100..700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&family=Playwrite+DE+Grund:wght@100..400&family=Raleway:ital,wght@0,100..900;1,100..900&family=Roboto+Mono:ital,wght@0,100..700;1,100..700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Lato:ital,wght@0,100;0,300;0,400;0,700;0,900;1,100;1,300;1,400;1,700;1,900&family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Arimo:ital,wght@0,400..700;1,400..700&display=swap');

.robotoFont{
  font-family: "Roboto Mono", monospace;
  font-optical-sizing: auto;
  font-style: normal;
}

.colorPicker{
    background-color: none;
    border: 1px solid red;
    border-bottom: 50px solid red;
    cursor: pointer;
    border-radius: 3px;
    width: 100px;
    justify-content: center;
    align-items: center;
    text-align: center;
    user-select: none;
    font-weight: 300
}

.clr-field button{
  display: none;
}

.monserrat{
  font-family: "Montserrat", sans-serif;
  font-optical-sizing: auto;
  font-weight: 400;
  font-style: normal;
}

.Ralway{
  font-family: "Raleway", sans-serif;
  font-optical-sizing: auto;
  font-weight: 400;
  font-style: normal;
}

.playwrite{
  font-family: "Playwrite DE Grund", cursive;
  font-optical-sizing: auto;
  font-weight: 200;
  font-style: normal;
}

.lato{
  font-family: "Lato", sans-serif;
  font-weight: 400;
  font-style: normal;
}

.Arimo{
  font-family: "Arimo", sans-serif;
  font-optical-sizing: auto;
  font-weight: 400;
  font-style: normal;
}

.colorPalette {
  border: 1px solid rgba(0, 0, 0, 0.199);
  width: 120px;
  height: 140px;
  border-radius: 6px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: safe;
  font-size: 20px;
  font-weight: lighter;
  color: #505050;
  margin: 1em
}

.colorDiv{
  display: flex
}

.colorPalette input{
  border: 0px;
  width: 118px;
  height: 90px;
  border-radius: 6px;
  outline: none;
  cursor: pointer
}

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
  margin-left: 0px;
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
  flex-direction: column
}

.dataInputs{
  margin-top: 1em;
  margin-bottom: 1em
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
  justify-content: space-between;
  align-items: center;
  display: flex;
}

.liData{
  display: flex;
  flex-direction: column;
}

.liData{
  margin-bottom: 0.4em
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
    justify-content: left;
    flex-wrap: wrap;
}

.step1 {
    display: flex;
    flex-direction: column;
    justify-content: left;
    align-items: left;
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