<template>
  <header>
    <div class="langimg">
      <img id="sweimg" src="/img/sweflag.png" v-on:click="switchLanguage('sv')">
      <img id="ukimg" src="/img/ukflag.png" v-on:click="switchLanguage('en')">
    </div>
    <div>
      <img v-if="showMysteryButton" class="mysteryButton" @click="toggleMusic" src="/img/playbutton.png"
        alt="Toggle Mute" />
      <img class="muteButton" @click="toggleMute" :src="buttonImage" alt="Toggle Mute" />
    </div>
  </header>
  <main>
    <section id="section1">
      <img id="brake" src="/img/brake.png">
      <h1>{{ uiLabels.heading }}</h1>
      <div>
        <img id="earth" src="/img/spinning_globe.gif">
      </div>
      <h2>{{ uiLabels["sales-pitch2"] }}</h2>
    </section>
    <section class="button-container">
      <router-link to="/create/"><button id="createbutton"> {{ uiLabels.createGame }}</button></router-link>
      <router-link to="/howtoplay/"><button id="howtoplaybutton"> {{ uiLabels.about }}</button></router-link>
      <router-link to="/playerjoin/"><button id="joinbutton"> {{ uiLabels.joinQuiz }}</button></router-link>
    </section>
  </main>
</template>

<script>
import io from 'socket.io-client';
import pressToMuteImage from "/img/soundon.png";
import pressToUnmuteImage from "/img/soundoff.png";
//sessionStorage.setItem("localhost","192.168.0.101:3000"); //Villes wifi
//sessionStorage.setItem("localhost","192.168.0.33:3000"); //Alicias Wifi
//sessionStorage.setItem("localhost", "94.191.152.228:3000"); //Elias mobilnät
//sessionStorage.setItem("localhost", "172.225.69.147"); //Villes mobilnät
// sessionStorage.setItem("localhost","172.20.10.2:3000"); //Alles wifi
sessionStorage.setItem("localhost", "localhost:3000");
const socket = io(sessionStorage.getItem("localhost"));

export default {
  name: 'StartView',
  components: {
  },
  data: function () {
    return {
      uiLabels: {},
      lang: localStorage.getItem("lang") || "en",
      isMuted: false,
      showMysteryButton: true,
    }
  },
  computed: {
    buttonImage() {
      return this.isMuted ? pressToUnmuteImage : pressToMuteImage;
    }
  },
  created: function () {
    socket.emit("pageLoaded", this.lang);
    socket.on("init", (labels) => {
      this.uiLabels = labels
    });
    const isMuted = sessionStorage.getItem("isMuted");
    if (isMuted) {
      this.isMuted = JSON.parse(isMuted);
    }
  },
  methods: {
    switchLanguage: function (lang) {
      this.lang = lang;
      localStorage.setItem("lang", this.lang);
      socket.emit("switchLanguage", this.lang)
    },
    toggleMusic() {
      const audioPlayer = this.$root.$refs.audioPlayer;
      audioPlayer.play();
      this.showMysteryButton = false;
    },
    toggleMute() {
      const audioPlayer = this.$root.$refs.audioPlayer;
      audioPlayer.muted = !audioPlayer.muted;
      this.isMuted = !this.isMuted;
      sessionStorage.setItem("isMuted", JSON.stringify(this.isMuted));
    },
  }
}
</script>

<style scoped>
body {
  overflow: hidden;
}

#brake {
  margin-top: -1vw;
  width: 15vw;
}

#earth {
  margin-top: -6vw;
  margin-bottom: -6vw;
  width: 17vw;
}

.langimg {
  margin-top: 15px;
  position: fixed;
  display: flex;
  justify-content: space-between;
}

#sweimg {
  margin-left: 20px;
  margin-right: 10px;
  width: 4vw;
}

#ukimg {
  margin-right: 10px;
  width: 4vw;
}

.button-container {
  margin-top: -1em;
  bottom: 0;
  left: 0;
  width: calc(100% - 4em);
  display: flex;
  justify-content: space-between;
  padding: 2em;
}

.button-container button {
  pointer-events: auto;
}

.button-container {
  pointer-events: none;
}

#createbutton {
  margin-left: 15vw;
  background-color: green;
}

#howtoplaybutton {
  margin-left: 2vw;
  margin-right: 1vw;
  width: 10em;
  background-color: green;
}

#joinbutton {
  margin-right: 15vw;
  background-color: green;
}

h2 {
  margin-top: 6vw;
  font-style: italic;
  font-size: 1.4vw;
}

@media screen and (max-width: 800px) {

  #brake {
    margin-top: 5vw;
    width: 40vw;
  }


  #sweimg {
    margin-left: 20px;
    margin-right: 10px;
    width: 8vw;
  }

  #ukimg {
    margin-right: 10px;
    width: 8vw;
  }

  #earth {
    margin-top: -10vw;
    margin-bottom: -5vw;
    width: 40vw;
  }


  h1 {
    font-size: 12vw;
    margin-top: 0vh;
  }

  h2 {
    margin-top: 5vh;
    font-style: italic;
    font-size: 3vw;
  }

  .button-container {
    flex-direction: column;
    align-items: center;
  }

  #createbutton,
  #howtoplaybutton,
  #joinbutton {
    margin: 4vw 0;
    width: 75vw;
    font-size: 7vw;
    border: 0.3vw solid black;
  }
}
</style>