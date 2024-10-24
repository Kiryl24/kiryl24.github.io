<template>
  <div id="app">
    <header :class="{ scrolled: isScrolled }">
      <div class="header-content">
        <img src="@/assets/logo.png" alt="Logo JK Studio" class="logo" @click="scrollToTop" />
        <div class="header-text">
          <h1 class="jk-text">
            <span class="color-j">J</span>
            <span class="color-k">K</span>
          </h1>
          <h1 class="studio-title">
            <span class="color-studio">
              STUDIO<span :style="{ fontSize: '0.75em', verticalAlign: 'super' }">&copy;</span>
            </span>
          </h1>
        </div>
      </div>
      <nav>
        <a href="#about">O NAS</a>
        <a href="#offer">OFERTA</a>
        <a href="#cooperation">WSPÓŁPRACA</a>
        <a href="#contact">KONTAKT</a>
      </nav>
    </header>

    <section id="about">
      <h2 @click="toggleSection('about')">O NAS</h2>
      <img src="@/assets/keyboard.jpg" alt="O nas - Keyboard" @click="toggleSection('about')" />
      <transition @enter="enter" @leave="leave">
        <div ref="about" v-if="sections.about" class="details">
          <p>Projekt ten został rozpoczęty podczas spontanicznego spotkania, które miało na celu stworzenie utworu łączącego kompozycję klasyczną wraz z charakterem współczesnej muzyki rapowej.</p>
          <p>
            <a
                href="https://on.soundcloud.com/YKexJovTZt4nQ1bX7"
                target="_blank"
                rel="noopener noreferrer"
                style="color: #e76b00; text-decoration: underline;"
            >
              Soundcloud kompozytora
            </a>
          </p>
          <AudioPlayer
              v-for="(track, index) in tracks"
              :key="index"
              :src="track.src"
              :title="track.title"
              :isActive="currentTrack === index"
              @play="handlePlay(index)"
              @pause="handlePause(index)"
              ref="player"
          />
        </div>
      </transition>
    </section>


    <section id="offer">
      <h2 @click="toggleSection('offer')">OFERTA</h2>
      <img src="@/assets/microphone.jpg" alt="Oferta - Microphone" @click="toggleSection('offer')" />
      <transition @enter="enter" @leave="leave">
        <div ref="offer" v-if="sections.offer" class="details">
          <p>Oferujemy tworzenie autorskich beatów na zamówienie. Wszystkie parametry takie jak nastrój, tonacja, długośc oraz dynamika mogą byc ustalone poprzez kontakt.</p>
        </div>
      </transition>
    </section>

    <section id="cooperation">
      <h2 @click="toggleSection('cooperation')">WSPÓŁPRACA</h2>
      <img src="@/assets/studio.jpg" alt="Współpraca - Studio" @click="toggleSection('cooperation')" />
      <transition @enter="enter" @leave="leave">
        <div ref="cooperation" v-if="sections.cooperation" class="details">
          <p>Chętnie poszerzymy swoje horyzonty poprzez tworzenie utworów z innymi artystami. Gwarantujemy oryginalnośc oraz niezawodnośc w tym co robimy.</p>
        </div>
      </transition>
    </section>

    <section id="contact">
      <h2 @click="toggleSection('contact')">KONTAKT</h2>
      <img src="@/assets/contact.jpg" alt="Kontakt" @click="toggleSection('contact')" />
      <transition @enter="enter" @leave="leave">
        <div ref="contact" v-if="sections.contact" class="details">
          <p> E-Mail kontaktowy: jkstudiomusic.contact@gmail.com</p>
          <p>Telefon: +48 570 285 788</p>
        </div>
      </transition>
    </section>
  </div>
</template>

<script>
import AudioPlayer from './components/AudioPlayer.vue';

export default {
  name: 'App',
  components: {
    AudioPlayer,
  },
  data() {
    return {
      sections: {
        about: false,
        offer: false,
        cooperation: false,
        contact: false,
      },
      tracks: [
        { src: require('@/assets/audio/Piano Concertino No.2 1st Mvt.mp3'), title: 'Kompozycja autorska: Piano Concertino No.2 1st Mvt' },
        { src: require('@/assets/audio/Piano Concertino No.2 2nd Mvt.mp3'), title: 'Kompozycja autorska: Piano Concertino No.2 2nd Mvt' }
        /* { src: require('@/assets/audio/Far from home.mp3'), title: 'Far from home' }, */
      ],
      currentTrack: null,
      isScrolled: false, // Kontrola zmiany tła nagłówka przy przewijaniu
    };
  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll);
  },
  methods: {
    toggleSection(section) {
      this.sections[section] = !this.sections[section];
    },
    handlePlay(index) {
      if (this.currentTrack !== null && this.currentTrack !== index) {
        this.$refs.player[this.currentTrack].pauseAudio();
      }
      this.currentTrack = index;
    },
    handlePause(index) {
      if (this.currentTrack === index) {
        this.currentTrack = null;
      }
    },
    // Scroll to top when logo is clicked
    scrollToTop() {
      window.scrollTo({
        top: 0,
        behavior: 'smooth' // Płynne przewijanie
      });
    },
    // Handle scroll to apply class for sticky header
    handleScroll() {
      this.isScrolled = window.scrollY > 50;
    },
    // Animacje otwierania sekcji
    enter(el) {
      el.style.height = '0';
      el.style.transition = 'height 0.5s ease';
      el.offsetHeight; // Trigger reflow
      el.style.height = el.scrollHeight + 'px';
    },
    leave(el) {
      el.style.height = el.scrollHeight + 'px';
      el.offsetHeight; // Trigger reflow
      el.style.transition = 'height 0.5s ease';
      el.style.height = '0';
    }
  },
  beforeUnmount() {
    window.removeEventListener('scroll', this.handleScroll);
  }
};
</script>

<style scoped>
header {
  position: sticky;
  top: 0;
  z-index: 1000;
  background-color: #e0e0e0;
  padding: 20px;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between; /* Rozkłada elementy w poziomie */
}

.header-content {
  display: inline-flex;
  align-items: center;
  width: 100%;
  max-width: 1200px; /* Maksymalna szerokość nagłówka */
  margin-left: 150px; /* Wyśrodkowanie nagłówka */

}

.logo {
  width: 50px;
  margin-right: 50px;
  cursor: pointer;
}

.header-text {
  display: flex;
  align-items: baseline;
  flex-direction: row; /* Ustawienie tekstu w kolumnie */
  margin-right: auto; /* Odpycha nawigację na prawo */
}

.jk-text, .studio-title {
  margin: 5px;

}

.jk-text {
  font-size: 2rem;
}


.studio-title {
  font-size: 2rem;
  vertical-align: baseline;
}

.color-j {
  color: #070707;
}

.color-k {
  color: #e76b00;
}

.color-studio {
  color: #070707;
}

/* Sticky header - when scrolled */
header.scrolled {
  background-color: rgba(224, 224, 224, 0.9);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); /* Delikatny cień */
}

/* Navigation styling */
nav {
  display: flex;
  justify-content: center;
  width: 100%;
}

nav a {
  color: black;
  text-decoration: none;
  margin: 0 15px;
  font-weight: bold;
}

nav a:hover {
  text-decoration: underline;
}

/* Section styling */
section {
  padding: 50px 5%;
  width: 100%;
  box-sizing: border-box;
}

/* Section headers */
h2 {
  font-size: 2rem;
  margin-bottom: 20px;
  text-transform: uppercase;
  cursor: pointer;
}

/* Section images */
img {
  width: 100%;
  height: auto;
  cursor: pointer;
  transition: transform 0.3s ease;
}

img:hover {
  transform: scale(1.05);
}

/* Details section - hidden by default */
.details {
  overflow: hidden;
  transition: height 0.5s ease;
  width: 100%;
  max-width: fit-content(90%);
  margin: 0 auto;
  padding: 20px;
  box-sizing: border-box;
  font-size: 1.5rem;
}

/* Media queries for smaller screens */
@media (max-width: 768px) {
  header {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .header-content {
    flex-direction: row;
    align-items: center;
    display: flex;
  }

  .logo {
    margin-bottom: 10px;
  }

  .jk-text, .studio-title {
    font-size: 1.5rem;
  }

  nav {
    flex-direction: column;
    align-items: center;
  }

  nav a {
    margin: 10px 0;
  }

  section {
    padding: 30px 5%;
  }

  .details {
    padding: 10px;
  }
}

html, body {
  margin: 0;
  padding: 0;
  overflow-x: clip;
  width: 100%;
}

#app {
  max-width: 100%;
  overflow-x: clip;
}
</style>
