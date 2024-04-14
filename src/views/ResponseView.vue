<script setup lang="ts">
import Card from "primevue/card";
import Image from 'primevue/image';
import {ref} from "vue";
// Import component
import Loading from 'vue3-loading-overlay';
import {useRoute} from "vue-router";
import axios from "axios";

const route = useRoute();
let topic = route.params.topic;


// Import stylesheet
const scenario = ref({
  title: '',
  content: ''
});
const isLoading = ref(true);

//@ts-ignore
function typeText(proxyRef, text: string, speed: integer, refKey: string) {
  let index = 0;

  function typeNextCharacter() {
    if (index < text.length) {
      proxyRef[refKey] += text.charAt(index);
      // console.log(proxyRef);
      index++;
      setTimeout(typeNextCharacter, speed);
    }
  }

  typeNextCharacter();
}
const image = ref(null);


const loadTopic = async () => {
  const { data } = await axios.post('https://topic-detector.bouzaabiarayen.workers.dev', {
        query: topic
      }
  )
  let texts = data.response.split("\n");
  let title = '';
  if(texts.length > 0){
    if(texts[0].includes('Title:')){
      title = texts[0].replace('Title:', '')
      texts.shift();
    }
  }
  let storyText = texts.join("\n");
  loadImage(storyText);
  scenario.value.title = '';
  typeText(scenario.value, title, 10, 'title'); // Adjust speed as needed

  scenario.value.content = '';
  typeText(scenario.value, storyText, 10, 'content'); // Adjust speed as needed

  isLoading.value = false;

}
// isLoading.value = false;


const  loadImage = async (storyText) => {
  image.value = '';
  const { data } = await axios.post('https://stable-diff.bouzaabiarayen.workers.dev', {
        story:storyText
      }, {
        responseType: 'blob'
      }
  )
  var reader = new FileReader();
  reader.onloadend = function() {
   // console.log(reader.result);
   image.value = reader.result;
  }
  reader.readAsDataURL(data);
};

loadTopic();



</script>
<template>
  <div class="vld-parent">
    <loading :active="isLoading"
             :is-full-page="true"></loading>
  </div>
  <div class="full-bg p-d-flex p-flex-column p-jc-between" v-if="!isLoading">
    <div class="background-image" :style="{ backgroundImage: `url(${image})` }" >
      <div class="overlay"></div>
    </div>
    <!-- Full background image -->
    <div
        class="main-content" style="height: 100vh;">

      <div class="">
        <div class="grid p-container">
          <div class="col-6 .col-md-6 .col-sm-12 sub-content">
            <!-- This content will be on the left for md screens and above -->

            <Card class="scenario">
              <template #title>{{ scenario.title }}</template>
              <template #content>
                <p class="m-0">
                 {{ scenario.content }}
                </p>
              </template>
            </Card>
          </div>
          <div class="col-6 .col-md-6 .col-sm-12 right-content sub-content">
            <!-- This content will be on the right for md screens and above -->
            <div class="text-right  p-3 border-round-sm text-white font-bold">
              <img :src="image" class="border-round-md right-img" alt="Image" v-if="image"/>
            </div>
          </div>
        </div>
      </div>

      
    </div>
  </div>
</template>


<style scoped>

.full-bg {
  position: relative;
  width: 100%;
  height: 100%;
}

.background-image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url(https://i.imgur.com/mM5oqpI.jpeg);
  background-size: cover;
  background-position: center;
  filter: blur(2px);
}


.small-image {
  max-width: 30%;
  border-radius: 0.25rem;
  position: relative;
  z-index: 1;
}

.tags {
  position: absolute;
  bottom: 0;
  width: 100%;
}

.p-tag {
  background-color: #e0e0e0;
  border-radius: 16px;
  padding: 0.5rem 1rem;
}

.p-container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem;
}

.right-img {
  max-width: 77%;
  max-height: 90%;
  width: auto;
  height: auto;
}

.scenario {
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  padding: 20px;
  transform: translateY(-50%);
}
.main-content{
  z-index: 2;
}
.right-content{
  z-index: 2;
}
.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5); /* Adjust the opacity as needed */
}


.vld-icon svg{
  margin-left: auto;
  margin-right: auto;
  display: block;
  margin-top: 10%;
}

.sub-content{
  display: flex;
  justify-content: center;
  align-items: center;
  max-height:100vh;
}
</style>