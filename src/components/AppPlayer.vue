<template>
  <div class="wrapper">
    <select v-model="harmonicOrMelodic">
      <option value="harmonic">Harmonic</option>
      <option value="melodic">Melodic</option>
    </select>
    <a class="play-button" v-if="samplesLoaded" @click.prevent="playInterval()">
      {{ playButtonText }}
    </a>

    <ul class="user-answer">
      <li
        v-for="(interval, name, index) in intervals"
        :key="index"
        :value="name"
        @click="checkAnswer($event)"
      >
        {{ interval }}
      </li>
    </ul>

    <div class="spoiler-box">
      <div v-if="showInterval" class="interval-display">
        {{ currentInterval }} - {{ note1 }} - {{ note2 }}
        <div v-if="userAnswerIsCorrect" class="is-correct">Correct!</div>
        <div v-if="!userAnswerIsCorrect" class="is-wrong">Wrong!</div>
      </div>
    </div>
  </div>
</template>

<script>
import { Howl, Howler } from "howler";
export default {
  name: "Player",
  props: {},
  data() {
    return {
      samplesLoaded: false,
      currentInterval: null,
      currentIntervalSemitones: null,
      note1: null,
      note2: null,
      showInterval: false,
      userAnswer: null,
      userAnswerIsCorrect: null,
      repeat: false,
      playButtonText: "Play",
      firstNote: "",
      secondNote: "",
      harmonicOrMelodic: "harmonic",
      note_degrees: {},
      intervals: {
        0: "Octave",
        1: "Minor Second",
        2: "Major Second",
        3: "Minor Third",
        4: "Major Third",
        5: "Perfect Fourth",
        6: "Tritone",
        7: "Perfect Fifth",
        8: "Minor Sixth",
        9: "Major Sixth",
        10: "Minor Seventh",
        11: "Major Seventh",
        12: "Octave"
      },
      notes: {
        C4: "C",
        CSharp4: "C#",
        D4: "D",
        DSharp4: "D#",
        E4: "E",
        F4: "F",
        FSharp4: "F#",
        G4: "G",
        GSharp4: "G#",
        A4: "A",
        ASharp4: "A#",
        B4: "B"
      },
      note_files: {}
    };
  },
  methods: {
    getInterval(note1, note2) {
      const notes = {
        C: 1,
        "C#": 2,
        D: 3,
        "D#": 4,
        E: 5,
        F: 6,
        "F#": 7,
        G: 8,
        "G#": 9,
        A: 10,
        "A#": 11,
        B: 12
      };
      if (notes[note1] > notes[note2]) {
        return 12 + notes[note2] - notes[note1];
      } else {
        return notes[note2] - notes[note1];
      }
    },
    selectRandomNote(prevNote = "none") {
      let randomNote;

      do {
        const notes = Object.keys(this.notes);
        randomNote = notes[(notes.length * Math.random()) << 0];
      } while (randomNote === prevNote);
      return randomNote;
    },
    playInterval() {
      if (!this.repeat) {
        // Select random note
        this.firstNote = this.selectRandomNote();
        this.secondNote = this.selectRandomNote(this.firstNote);
      }

      this.repeat = true;
      this.playButtonText = "Repeat";

      // Save representation of note name without octave
      const firstNoteName = this.notes[this.firstNote];
      const secondNoteName = this.notes[this.secondNote];

      // Play interval
      if (this.harmonicOrMelodic === "harmonic") {
        this.note_files[this.firstNote].play();
        this.note_files[this.secondNote].play();
      } else {
        this.note_files[this.firstNote].play();
        setTimeout(() => this.note_files[this.secondNote].play(), 1000);
      }

      // Getting interval in semitones
      this.currentIntervalSemitones = this.getInterval(
        firstNoteName,
        secondNoteName
      );
      // Readable interval
      this.currentInterval = this.intervals[this.currentIntervalSemitones];
      this.note1 = firstNoteName;
      this.note2 = secondNoteName;

      this.showInterval = false;
    },

    // Check user answer
    checkAnswer(event) {
      this.repeat = false;
      this.playButtonText = "Play";
      const userVal = parseInt(event.toElement.value);
      this.showInterval = true;
      userVal === this.currentIntervalSemitones
        ? (this.userAnswerIsCorrect = true)
        : (this.userAnswerIsCorrect = false);
    }
  },
  mounted() {
    const noteNames = Object.keys(this.notes);
    for (let note of noteNames) {
      this.note_files[note] = new Howl({
        src: ["http://127.0.0.1:8081/piano/" + note + ".mp3"],
        onloaderror: function(id, error) {
          console.error("loadError: " + note + " - " + error);
        }
      });
    }
    this.samplesLoaded = true;
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.play-button {
  cursor: pointer;
  border: 1px #55eca8 solid;
  color: #55eca8;
  border-radius: 50%;
  height: 7em;
  width: 7em;
  display: flex;
  justify-content: center;
  align-items: center;
  text-decoration: none;
  text-transform: uppercase;
  margin: 1em 0;
  transition: 0.3s all;

  &:hover {
    border: 1px #86ffc9 solid;
    color: #86ffc9;
  }
}

.user-answer {
  display: flex;

  li {
    cursor: pointer;
    text-decoration: underline;
    &:hover {
      color: #42b983;
    }
  }
}

.spoiler-box {
  height: 100px;
  width: 200px;
  background-color: #333;
  color: #ffffff;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 25px;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
