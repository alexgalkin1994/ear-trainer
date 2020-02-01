<template>
  <div class="wrapper">
    <button v-if="samplesLoaded" @click.prevent="playIntervalHarmonically()">
      Play Harmonically
    </button>
    <button @click="showInterval = true">Show Interval</button>

    <div class="spoilerBox">
      <div v-if="showInterval" class="intervalDisplay">
        {{ currentInterval }} - {{ note1 }} - {{ note2 }}
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
      note1: null,
      note2: null,
      showInterval: false,
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
        const keys = Object.keys(this.notes);
        randomNote = keys[(keys.length * Math.random()) << 0];
      } while (randomNote === prevNote);
      return randomNote;
    },
    playIntervalHarmonically() {
      // Select random note
      const firstNote = this.selectRandomNote();
      const secondNote = this.selectRandomNote(firstNote);

      // Save representation of note name without octave
      const firstNoteName = this.notes[firstNote];
      const secondNoteName = this.notes[secondNote];

      // Play interval
      this.note_files[firstNote].play();
      this.note_files[secondNote].play();

      // Readable interval
      this.currentInterval = this.intervals[
        this.getInterval(firstNoteName, secondNoteName)
      ];
      this.note1 = firstNoteName;
      this.note2 = secondNoteName;

      this.showInterval = false;
    }
  },
  mounted() {
    const noteNames = Object.keys(this.notes);
    for (let note of noteNames) {
      this.note_files[note] = new Howl({
        src: ["http://127.0.0.1:8081/" + note + "_Piano.mp3"],
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

button {
  padding: 25px;
  margin: 25px;
}

.spoilerBox {
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
