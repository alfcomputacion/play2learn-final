<template>
  <div class="container">
    <TheHeader @goto-page="changePage" />
       <!-- We condition the page 
          any time page changes it goes 
               to the right page -->
             
        <div v-if="page === 'mathificent'" class="mathificent">
          <TheMathificent />
        </div>

        <div v-else-if="page === 'anagram'" class="anagram">
          <AnagramHunt />
        </div>      
        <div  v-else-if="page === 'home'">
          <div class="header-space"></div>
          <TheQuotes  @goto-page="changePage"/>
          
        </div>
      <div v-if="page === 'contactus'" class="contactus">
          <ContacUs />
      </div>
      <div v-if="page === 'login'" class="login">
        <LogSignIn />
      </div>
      <div v-if="page === 'aboutus'">
        <AboutUs />
      </div>
 <div class="bottom-space"></div>
    <TheFooter @goto-page="changePage"/>
  </div>
</template>

<script>

import TheMathificent from '../components/TheMathificent.vue';
import TheHeader from '../components/TheHeader.vue';
import AnagramHunt from '../components/AnagramHunt.vue';
import TheQuotes from '../components/TheQuotes.vue';
import TheFooter from '../components/TheFooter.vue';
import ContacUs from '@/views/ContacUs.vue';
import LogSignIn from './LogSignIn.vue'
import AboutUs from '@/views/AboutUs.vue';

export default {
  name: 'App',
  data: function(){
    return{
      page: 'home'
    }
  },
  methods:{
    changePage(value){
    this.page = value
    }
  },
  
  components: {
    TheMathificent,
    TheHeader,
    AnagramHunt,
    TheQuotes,
    TheFooter,
    ContacUs,
    LogSignIn,
    AboutUs
}
}
</script>

<style>

.header-space{
  margin-top: 4rem;
}
.bottom-space{
margin-bottom: 7rem;
}

.spinner{
  margin: auto;
  border: 4px solid rgba(0, 0, 0, .1);
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border-left-color: #09f;
  animation: spin 1s linear infinite;
}
@keyframes spin {
  0%{
    transform: rotate(0deg);
  }
  100%{
    transform: rotate(360deg);
  }

  
}
/* GLOBAL SLIDE TRANSITIONS */
  .slide-leave-active,
  .slide-enter-active {
    position: absolute;
    top: 56px;
    transition: 1s;
    width: 380px;
  }

  .slide-enter {
    transform: translate(-100%, 0);
    transition: opacity .5s;
    filter: blur(2.5px);
  }

  .slide-leave-to {
    opacity:0;
    transform: translate(100%, 0);
    filter: blur(2.5px);
  }

  .slide-right-leave-active,
  .slide-right-enter-active {
    position: absolute;
    /* top: 56px; */
    transition: 1s;
    width: 380px;
    filter: blur(2.5px);
  }

  .slide-right-enter {
    transform: translate(100%, 0);
    transition: opacity .5s;
    filter: blur(2.5px);
  }

  .slide-right-leave-to {
    opacity:0;
    transform: translate(-100%, 0);
    filter: blur(2.5px);
  }
    .big {
    font-size: 1.5em;
  }
  .bigger{
    font-size: 2em;
  }

  .huge {
    font-size: 5em;
    font-weight: 600;
    color: #09f;
  }

  /*#region ANIMATION  */
#game-points{
    display: none;
    opacity: 0;
    text-shadow: 0 0 0.01rem black;
}

#game-points{
    position: absolute;
    display: flex;
    top: 40%;
    left: 75%;
    color: yellow;
    z-index: -1000;

}

.animate-minimize{
    animation: scaledown 2s ease;
}
@keyframes scaledown {
    0%{
        z-index: 1000;
        transform: scale(8);
        opacity: 1;
    }
  
    100%{
        z-index: 1;
        transform: scale(1);
        opacity: 0;
    }
}
/* } */
/*#endregion ANIMATION  */
</style>>