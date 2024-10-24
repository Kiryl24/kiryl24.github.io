<template>
  <div class="audio-player">
    <div class="audio-info">
      <h3>{{ title }}</h3>
    </div>
    <div class="audio-controls">
      <img v-if="!isPlaying" src="@/assets/playbutton.png" @click="playAudio" class="control-button" alt="Play" />
      <img v-if="isPlaying" src="@/assets/stopbutton.png" @click="pauseAudio" class="control-button" alt="Pause" />
      <input
          type="range"
          class="progress-slider"
          :min="0"
          :max="duration"
          :value="currentTime"
          @input="seekAudio($event)"
      />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    src: String,
    title: String,
    isActive: Boolean,
  },
  data() {
    return {
      audio: new Audio(this.src),
      isPlaying: false,
      currentTime: 0,
      duration: 0,
      volume: 1.0,
    };
  },
  mounted() {
    this.audio.volume = this.volume;
    this.audio.addEventListener('timeupdate', this.updateTime);
    this.audio.addEventListener('loadedmetadata', this.updateDuration);
    this.updateProgressGradient();

    if (this.isActive) {
      this.playAudio();
    }

    document.addEventListener('visibilitychange', this.handleVisibilityChange);
  },
  methods: {
    playAudio() {
      if (!this.isPlaying) {
        this.audio.play().catch(error => {
          console.error("Audio playback failed:", error);
        });
        this.isPlaying = true;
        this.$emit('play', this);
      }
    },
    pauseAudio() {
      if (this.isPlaying) {
        this.audio.pause();
        this.isPlaying = false;
        this.$emit('pause', this);
      }
    },
    seekAudio(event) {
      this.audio.currentTime = event.target.value;
      this.currentTime = this.audio.currentTime;
      this.updateProgressGradient();
    },
    updateTime() {
      this.currentTime = this.audio.currentTime;
      this.updateProgressGradient();
    },
    updateDuration() {
      this.duration = this.audio.duration;
    },
    updateProgressGradient() {
      const progressSlider = this.$el.querySelector('.progress-slider');
      const value = (this.currentTime / this.duration) * 100;
      progressSlider.style.background = `linear-gradient(to right, #e76b00 ${value}%, #cdcdcd ${value}%, #cdcdcd 100%)`;
    },
    handleVisibilityChange() {
      if (document.hidden) {
        this.pauseAudio();
      }
    },
  },
  beforeUnmount() {
    this.audio.removeEventListener('timeupdate', this.updateTime);
    this.audio.removeEventListener('loadedmetadata', this.updateDuration);
    this.audio.pause();
    document.removeEventListener('visibilitychange', this.handleVisibilityChange);
  },
};
</script>

<style scoped>
.audio-player {
  display: flex;
  align-items: center; /* Wyśrodkowanie wzdłuż osi pionowej */
  padding: 10px;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  box-sizing: border-box;
}

.audio-info {
  flex: 1; /* Rozciąganie bloku tytułu, aby wypełniał dostępne miejsce */
}

.audio-controls {
  display: flex;
  flex-direction: row;
  align-items: center; /* Wyrównanie elementów do prawej strony */
  gap: 10px; /* Odstęp między elementami kontrolkami */
  margin-left: 5px; /* Odstęp od tytułu */
  width: 70%;
}

.control-button {
  width: 40px; /* Przyciski dla lepszej klikalności */
  height: 40px;
  cursor: pointer;
  transition: transform 0.3s ease; /* Płynne przejście dla transformacji */
}

.control-button:hover {
  transform: scale(1.2); /* Powiększenie przycisku na hover */
}

.progress-slider {
  -webkit-appearance: none;
  background: linear-gradient(to right, #e76b00 0%, #cdcdcd 100%);
  height: 8px; /* Cienki suwak */
  border-radius: 5px;
  width: 100%; /* Pełna szerokość kontenera */
  max-width: 600px; /* Maksymalna szerokość suwaka */
  box-sizing: border-box; /* Uwzględnia padding i border w szerokości */
  cursor: pointer;
}

.progress-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  background: #070707;
  height: 15px;
  width: 15px;
  border-radius: 50%;
  cursor: pointer;
  position: relative;
  transition: transform 0.3s ease; /* Płynne przejście dla transformacji */
}

.progress-slider::-webkit-slider-thumb:hover {
  transform: scale(1.5); /* Powiększenie znacznika położenia na hover */
}

.progress-slider::-moz-range-thumb {
  background: #070707;
  height: 15px;
  width: 15px;
  border-radius: 50%;
  cursor: pointer;
  transition: transform 0.3s ease; /* Płynne przejście dla transformacji */
}

.progress-slider::-moz-range-thumb:hover {
  transform: scale(1.5); /* Powiększenie znacznika położenia na hover */
}

/* Stylowanie mobilne */
@media (max-width: 768px) {
  .control-button {
    width: 50px; /* Większe przyciski dla urządzeń mobilnych */
    height: 50px;
  }

  .control-button:hover {
    transform: scale(1.3); /* Większe powiększenie na mobilnych urządzeniach */
  }

  .progress-slider {
    width: 90%; /* Dopasowanie suwaka do mniejszych ekranów */
  }
}
</style>
