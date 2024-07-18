<template>
  <div class="form-floating">

    <Transition>
      <div class="notificacion" v-if="errorForm">
        <span>Necesitas rellenar todos los campos.</span>
      </div>
    </Transition>
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

          <div class="d-flex bloqueSpacing">
            <button class="btn btn-danger me-4" @click="confirmEditLink(oldLinkId)">Confirmar</button>
            <button class="btn btn-warning" @click="cancelEditLink">Cancelar</button>
          </div>
        </div>
      </div>

  <div class="">
    <span>Configuración de el reproductor:</span>
  </div>

  <div class="bloqueSpacingSpecial" v-if="editionLinkMode === false">
    <div>
      <div class="form-check form-check-inline">
        <input 
        class="form-check-input" 
        type="radio" 
        name="inlineRadioOptions" 
        id="inlineRadio1"
        @click="selectingMode('radio')"
        checked
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

      <div class="metadataInputs mt-4">
        <span class="infoText">Campos obligatorios: Estos datos son necesarios para el funcionamiento del player.</span>
      </div>
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
          placeholder="Eslogan de la radio"
          :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"
          v-model="defaultSlogan">
        </div>

        <div class="metadataInputs">
              <input
              class="form-control mt-3" 
              type="file" 
              id="formFile" 
              accept="image/*"
              @change="previewfile">
        </div>

        <div class="metadataInputs">
          <img class="previewImage mt-3" v-show="preview" width="130" height="130" :src="preview">
        </div>

        <div class="metadataInputs">
          <input class="form-control dataInputs" 
            v-model="mainStreaming" 
            placeholder="Streamin Principal" 
            :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"/>
        </div>
        
        <div class="linkRadios">
          <span class="infoText">Campos opcionales: streaming de backups, metadata y programación.</span>
          <div class="linkInput">
            
            <div class="metadataInputs">
              <input class="form-control dataInputs" 
              v-model="linkInput" 
              placeholder="Streaming de backup" 
              :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"/>
            </div>

            <button class="btn btn-danger" @click="pushLink">Añadir enlace de backup</button>
          </div>
          <ul>
            <li 
            class="linkLi" 
            v-for="(link, index) in currentLinkList" 
            :key="index"
            >
            <div class="liData">
              <input v-model="link.link" class="form-control dataInputs">
            </div>
            <div class="LiPanel">
              <div class="upDown">
                <UpIcon 
                @click="changeLinkPosition('up', link.link, index)"></UpIcon>
              </div>
              <TrashIcon 
              class="trashIcon"
              @click="deleteLink(link.link)"></TrashIcon>
            </div>
          </li>
          </ul>
        </div>
      
    </div>

    <div class="bloqueSpacing InfoDiv">

      <span class="infoText">Datos de la Radio.</span>

      <input class="form-control dataInputs" 
            v-model="metadataInput" 
            placeholder="Enlace de Metadata" 
            :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"/>

      <input class="form-control dataInputs" 
      v-model="programmingInput" 
      placeholder="Enlace de Programacion" 
      :disabled="radioMode == 'radio' && stations.length > 0 && editionMode == false"/>
    </div>

  <div class="bloqueSpacing">

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

  <div class="bloqueSpacing">
    <div class="form-floating">
      <select class="form-select" v-model="fontTheme" id="floatingSelect" aria-label="Floating label select example">
        <option value="" selected disabled hidden>Poppins</option>
        <option value="Poppins" class="poppins-regular">Poppins</option>
        <option value="Roboto Mono" class="robotoFont">Roboto Mono</option>
        <option value="Montserrat" class="monserrat">Montserrat</option>
        <option value="Raleway" class="Ralway">Raleway</option>
        <option value="Playwrite DE Grund" class="playwrite">Play Write</option>
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
    >{{ radioMode == 'radio' ? 'Guardar Radio' : 'Añadir Nueva Radio' }}</button>
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

  <div class="stationsList" v-show="editionMode === false || radioMode === 'multiradio'">
      <div class="card stationCard" v-for="(station, index) in stations" :key="station.station_name">
        <div class="buttonsStations">
          <TrashIcon @click="deleteStation(station.station_name)"></TrashIcon>
          <EditIcon @click="editStation(station.station_name)"></EditIcon>
          <LeftIcon @click="changeRadioPosition(index)"></LeftIcon>
        </div>
        <img :src="station.imgPreview" width="44" height="44" class="radioImgPreview">
        <p>Nombre de la radio: {{ station.station_name }}</p><br/>
        <p>Numero de enlaces: {{ station.station_links.length }}</p><br/>
      </div>
  </div>

  <div class="d-grid gap-2 bloqueSpacing" v-if="stations.length > 0 && editionMode === false">
    <button 
    class="btn btn-danger" 
    type="button" 
    @click="generatePlayer"
    v-if="loading !== true"
    >Generar Reproductor</button>
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
    const radioMode = ref('radio');
    const metadataOn = ref(false);
    const metadataInput = ref('');
    const programmingInput = ref('');
    const preview = ref();
    const mainStreaming = ref(); // Main Streaming
    const loading = ref(false);
    const defaultSlogan = ref(''); // Eslogan por default
    const logoOrCover = ref(1);
    const jsonMedia = ref(''); // Json de la metadata
    const typeOfPlayer = ref('vertical');
    const codeview = ref(false);
    const paramToPlayer = ref('');
    const metadataLink = ref('');
    const editionMode = ref(false);
    const oldStationName = ref('');
    const mode = ref('vertical');
    const slim = ref(require('../assets/slim.png'));
    const vertical = ref(require('../assets/vertical.png'));
    const minimal = ref(require('../assets/minimal.png'));
    const colorTheme = ref('#cd2327');
    const colorTheme2 = ref('#e2e2e2')
    const defaultImage = ref('');
    const fontTheme = ref('Poppins');
    const errorForm = ref(false);

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
        }

        currentLinkList.value.push(radio)
        linkInput.value = '';
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
      errorForm.value = false
      currentLinkList.value.unshift({ link: mainStreaming.value})
      const station = {
        station_name: currentStationToAdd.value,
        station_links: currentLinkList.value,
        metadata: metadataInput.value,
        slogan: defaultSlogan.value,
        imgPreview: preview.value,
        cover: defaultImage.value
      }

      console.log(station)
      if(currentLinkList.value !== "" && currentStationToAdd.value !== "" && defaultSlogan.value !== ""){
        stations.value.push(station)
        currentLinkList.value = [];
        currentStationToAdd.value = '';
      }else{
        errorForm.value = true
        setTimeout(function(){
          errorForm.value = false ;
        }, 5000);
      }

      // Reiniciar datos en el formulario
      defaultSlogan.value = ''
      jsonMedia.value = ''
      metadataInput.value = ''
      mainStreaming.value = ''
      preview.value = ''
      defaultImage.value = null
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
      defaultImage.value = event.target.files[0]
      preview.value = URL.createObjectURL(event.target.files[0])
    }

    const generatePlayer = async () => {
      loading.value = true;

      const newConfig = {
        multiradio: radioMode.value === 'radio' ? 1 : 2,
        metadata: metadataOn.value === true ? 'metadata' : 'nometadata',
        default_slogan: defaultSlogan.value,
        logo_img: defaultImage.value,
        json_cover: jsonMedia.value,
        color: colorTheme.value,
        color2: colorTheme2.value,
        font: fontTheme
      }

      config.value = newConfig;

      const data = new FormData() 

      stations.value.forEach((val, index) => {
        data.append('covers', val.cover, val.station_name + index)
      })
      data.append('station', JSON.stringify(stations.value))
      data.append('config', JSON.stringify(config.value))  

      const url = 'https://player-radio-backend.inovanex.com/create';
      //const url = 'http://localhost:3000/create'
      console.log(data)

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
      oldLinkId,
      fontTheme,
      errorForm,
      mainStreaming
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

.notificacion{
  position: fixed;
  top: 2em;
  left: 35%;
  padding: 2em;
  border-radius: 20px;
  background-color: white;
  text-align: center;
  box-shadow: 0px 2px 14px 0px rgba(0,0,0,0.75);
  -webkit-box-shadow: 0px 2px 14px 0px rgba(0,0,0,0.75);
  -moz-box-shadow: 0px 2px 14px 0px rgba(0,0,0,0.75);
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

.poppins-regular {
  font-family: "Poppins", sans-serif;
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

.bloqueSpacing{
  margin-top: 3.5em;
  margin-bottom: 3.5em;
}

.bloqueSpacingSpecial{
  margin-top: 2.3em;
  margin-bottom: 3.5em;
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
  margin-top: 0em
}

.infoText{
  font-size: 14px;
  color: #505050c4;
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

.v-enter-active {
  transition: all 0.3s ease-out;
}

.v-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.v-enter-from,
.v-leave-to {
  transform: translateY(-20px);
  opacity: 0;
}

.InfoDiv {
  max-width: 400px;
}

.radioImgPreview{
  border-radius: 5px;
  margin-bottom: 1em;
}
</style>