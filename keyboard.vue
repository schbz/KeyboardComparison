<template>
  <div
    class="h-full min-h-screen flex my-auto w-auto bg-gradient-to-br from-emerald-300 to-cyan-300"
  >
    <div
      class="w-full min-h-full font-mono flex-col justify-evenly flex container mx-auto"
    >
      <div class="h-auto w-full p-4 md:flex-row flex flex-none flex-col">
        <div
          class="squaresb flex items-center justify-center content-center mx-auto h-auto border-black border-4 shadow-xl rounded-md overflow-hidden max-w-full mb-2 md:p-4 sm:mr-6 sm:py-3 md:py-3 lg:w-auto md:min-w-1/3 sm:w-auto bg-gray-300"
        >
          <div
            class="text-3xl sm:text-4xl md:text-5xl ml-6 h-full mt-12 md:mt-4"
          >
            📐
          </div>
          <div class="flex text-center mr-16">
            <h2
              class="md:text-3xl text-2xl sm:text-3xl font-extrabold text-left leading-none"
            >
              Keyboard Layouts
              <span class="italic md:ml-12 ml-5 mb-2 underline">Compared</span>
            </h2>
          </div>
        </div>

        <div class="mt-1 items-center justify-center">
          <p class="text-sm sm:text-md md:text-lg">
            Have you ever wanted to compare some of the most popular key
            arrangements available for latin-script keyboards? Add text to the
            box below to get an estimate of horizontal finger travel distance
            for each layout alternative. Totals are based on a standard keyboard
            pitch (distance between key centers) of
            {{ avgdistmm }} mm.
          </p>
        </div>
      </div>
      <div class="h-1 md:h-3 w-full flex-none"></div>
      <div
        id="mainbox"
        class="md:flex-row flex flex-grow flex-col w-full max-w-screen"
      >
        <div class="p-3 w-full md:w-1/2 flex flex-col h-full">
          <textarea
            id="textareaid"
            ref="txt"
            v-model="text"
            class="opacity-80 form-control block w-full px-3 py-1.5 text-base text-gray-700 bg-white bg-clip-padding border border-solid border-gray-700 rounded transition ease-in-out focus:text-gray-700 focus:opacity-100 focus:border-blue-800 focus:outline-none"
            rows="7"
            placeholder="Type or paste text here."
          ></textarea>
          <div class="w-full h-auto">
            <span class="float-left text-md"
              ><span class="text-xl">{{ text.length }}</span> Chars
              <span class="text-xl ml-5"> {{ words }}</span> Words
            </span>
            <button
              class="float-right bg-blue-600 text-white w-20 h-8 mt-1 font-medium text-xl uppercase rounded shadow-md hover:shadow-lg active:bg-blue-700 hover:bg-blue-800 transition duration-150 ease-in-out"
              type="button"
              :class="{ 'cursor-not-allowed opacity-50': clearDisabled }"
              :disabled="clearDisabled"
              @click="Clear"
            >
              Clear
            </button>
            <div class="float-left max-w-full">
              <select
                v-model="selected"
                class="bg-white max-w-full mt-3"
                @change="selecting"
              >
                <option value="0">Load Sample Text</option>
                <option value="1">Declaration of Independence</option>
                <option value="2">This app's source code</option>
                <option value="3">3000 most used english words</option>
              </select>
              <span class="ml-3" v-if="loaded != 0">{{
                loaded === 1 ? "loading..." : "loaded"
              }}</span>
            </div>
            <div class="flex sm:flex-row w-full justify-between">
              <div class="mt-2">
                <span class="md:font-bold text-xs sm:text-sm md:text-lg"
                  >Pitch:
                </span>
                <span
                  class="md:font-bold text-xs sm:text-sm md:text-lg"
                  @mouseup="setPitch(18)"
                >
                  <input id="one" v-model="pitch" type="radio" value="1" />
                  <label class="ml-2" for="one">18 mm </label></span
                >
                <span
                  class="md:font-bold text-xs sm:text-sm md:text-lg"
                  @mouseup="setPitch(19)"
                >
                  <input id="two" v-model="pitch" type="radio" value="2" />
                  <label class="ml-2" for="two">19 mm</label></span
                >
              </div>
              <div class="mt-2 pl-1 w-auto text-md md:text-lg md: font-bold">
                <input
                  id="metricCheck"
                  v-model="metric"
                  type="checkbox"
                  name="vehicle1"
                  true-value="Metric"
                  false-value="Imperial"
                />
                <label for="metricCheck">
                  <span
                    class="md:font-bold text-sm md:text-lg ml-2"
                    :class="[{ 'line-through': metric == 'Imperial' }]"
                    >Metric</span
                  ></label
                >
              </div>
            </div>

            <br />
          </div>

          <div v-if="serious" class="w-full h-auto text-justify text-lg">
            Assuming the text entered is equal to your average total typed per
            day, you could be saving up to
            <span class="font-bold">{{
              Format(parseInt(Maxdistance - Mindistance) * 365)
            }}</span>
            of finger movements each year by switching from qwerty to a more
            ergonomic layout!
          </div>

          <div v-if="!serious" class="w-full h-auto text-lg text-justify">
            <span>
              Adjusting to a new layout can be time consuming, but if you
              frequently suffer from soreness after long typing sessions, it may
              be well worth the effort. Click the progress bar labels for more
              information on each layout alternative.
            </span>
          </div>
        </div>

        <div
          class="md:w-1/2 max-w-full px-3 transition-all duration-500 justify-evenly gap-5 py-3 flex-col flex mt-10 md:mt-1"
        >
          <div class="">
            <div class="mb-1 float-left font-bold text-gray-700 text-2xl">
              ⌨
              <a
                class="hover:underline"
                href="https://en.wikipedia.org/wiki/QWERTY"
                target="_blank"
                >Qwerty</a
              >
            </div>
            <div
              class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md"
            >
              <div
                class="bg-gray-600 h-full transition-all duration-500 text-lg font-medium text-gray-100 text-center p-0.5 leading-none prog"
                :style="{ width: (Qdistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Qdistance) }}
              </div>
            </div>
          </div>

          <div>
            <div class="mb-1 float-left font-bold text-rose-700 text-2xl">
              ⌨
              <a
                class="hover:underline"
                href="https://en.wikipedia.org/wiki/AZERTY"
                target="_blank"
                >Azerty</a
              >
            </div>
            <div
              class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md"
            >
              <div
                class="bg-rose-600 h-full transition-all duration-500 text-lg font-medium text-rose-100 text-center p-0.5 leading-none prog"
                :style="{ width: (Adistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Adistance) }}
              </div>
            </div>
          </div>

          <div>
            <div class="mb-1 float-left font-bold text-indigo-700 text-2xl">
              ⌨
              <a
                class="hover:underline"
                href="https://en.wikipedia.org/wiki/Dvorak_keyboard_layout"
                target="_blank"
                >Dvorak</a
              >
            </div>
            <div
              class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md"
            >
              <div
                class="bg-indigo-600 h-full transition-all duration-500 text-lg font-medium text-indigo-100 text-center p-0.5 leading-none prog"
                :style="{ width: (Ddistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Ddistance) }}
              </div>
            </div>
          </div>
          <div>
            <div class="mb-1 float-left font-bold text-emerald-700 text-2xl">
              ⌨
              <a
                class="hover:underline"
                href="https://en.wikipedia.org/wiki/Colemak"
                target="_blank"
                >Colemak</a
              >
            </div>
            <div
              class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md"
            >
              <div
                class="bg-emerald-600 h-full transition-all duration-500 text-lg font-medium text-emerald-100 text-center p-0.5 leading-none prog"
                :style="{ width: (Cdistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Cdistance) }}
              </div>
            </div>
          </div>

          <div class="mb-5">
            <div class="mb-1 float-left font-bold text-amber-700 text-2xl">
              ⌨
              <a
                class="hover:underline"
                href="https://workmanlayout.org/"
                target="_blank"
                >Workman</a
              >
            </div>
            <div
              class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md"
            >
              <div
                class="bg-amber-600 h-full transition-all duration-500 text-lg font-medium text-amber-100 text-center p-0.5 leading-none prog"
                :style="{ width: (Wdistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Wdistance) }}
              </div>
            </div>
          </div>
        </div>
      </div>

      <div
        class="w-full h-auto mt-4 pb-5 justify-center font-mono flex text-sm sm:text-md"
      >
        <div class="text-center">
          <div class="flex flex-col sm:flex-row gap-1">
            <div><span>Created using the Dvorak layout </span><br /></div>
            <div class="flex-row items-center">
              <span class="ml-1"
                >by <router-link to="/">Schuyler</router-link></span
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      text: "",
      words: 0,
      clearDisabled: true,
      Qhomerow: ["a", "s", "d", "f", "j", "k", "l", ";"],
      Ahomerow: ["q", "s", "d", "f", "j", "k", "l", "m"],
      Dhomerow: ["a", "o", "e", "u", "h", "t", "n", "s"],
      Chomerow: ["a", "r", "s", "t", "n", "e", "i", "o"],
      Whomerow: ["a", "s", "h", "t", "n", "e", "o", "i"],
      avgdistmm: 18,
      Qdistance: 0,
      Adistance: 0,
      Ddistance: 0,
      Cdistance: 0,
      Wdistance: 0,
      totaldistance: 0,
      spacebardist: 0,
      homerowdist: 0,
      wordCount: 0,
      Maxdistance: 0,
      Mindistance: 0,
      sample: " default",
      selected: 0,
      loaded: 0,
      pitch: 1,
      metric: "Metric",
    };
  },
  mounted() {
    document
      .querySelector('meta[name="description"]')
      .setAttribute(
        "content",
        "Calculate and compare relative ergonomic advantage of various keyboard layouts, Qwerty, Azerty, Dvorak, Colemak, Workman"
      );
    document.title = "Keyboard Layouts Compared";
  },
  computed: {
    serious() {
      return this.Maxdistance - this.Mindistance > 300;
    },
  },
  watch: {
    text(newText, oldText) {
      this.Count();
    },
  },
  methods: {
    setPitch(num) {
      this.avgdistmm = num;
      this.Count();
    },
    selecting() {
      if (this.selected !== "0") {
        this.loaded = 1;

        this.getSample(this.selected);
      }
    },
    Count() {
      this.Qdistance = 0;
      this.Adistance = 0;
      this.Ddistance = 0;
      this.Cdistance = 0;
      this.Wdistance = 0;
      this.words = 0;

      for (let i = 0; i <= this.text.length; i++) {
        if (this.text.charAt(i) === " " && this.text.charAt(i - 1) !== " ") {
          this.words++;
          this.Qdistance += this.spacebardist * 2;
          this.Adistance += this.spacebardist * 2;
          this.Ddistance += this.spacebardist * 2;
          this.Cdistance += this.spacebardist * 2;
          this.Wdistance += this.spacebardist * 2;
        } else if (
          this.text.charAt(i) === " " &&
          this.text.charAt(i - 1) === " "
        ) {
          this.Qdistance += this.spacebardist * 2;
          this.Adistance += this.spacebardist * 2;
          this.Ddistance += this.spacebardist * 2;
          this.Cdistance += this.spacebardist * 2;
          this.Wdistance += this.spacebardist * 2;
        } else {
          if (!this.Qhomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Qdistance += this.avgdistmm * 2;
            }
          } else {
            this.Qdistance += this.homerowdist * 2;
          }
          if (!this.Ahomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Adistance += this.avgdistmm * 2;
            }
          } else {
            this.Adistance += this.homerowdist * 2;
          }
          if (!this.Dhomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Ddistance += this.avgdistmm * 2;
            }
          } else {
            this.Ddistance += this.homerowdist * 2;
          }
          if (!this.Chomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Cdistance += this.avgdistmm * 2;
            }
          } else {
            this.Cdistance += this.homerowdist * 2;
          }
          if (!this.Whomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Wdistance += this.avgdistmm * 2;
            }
          } else {
            this.Wdistance += this.homerowdist * 2;
          }
        }
      }
      this.totaldistance =
        this.Qdistance +
        this.Ddistance +
        this.Cdistance +
        this.Wdistance +
        this.Adistance;

      if (this.text !== "") {
        this.clearDisabled = false;
      } else {
        this.clearDisabled = true;
      }

      this.Maxdistance = Math.max(
        this.Qdistance,
        this.Adistance,
        this.Ddistance,
        this.Cdistance,
        this.Wdistance
      );
      this.Mindistance = Math.min(
        this.Qdistance,
        this.Adistance,
        this.Ddistance,
        this.Cdistance,
        this.Wdistance
      );
    },

    Clear() {
      this.clearDisabled = true;
      this.words = 0;
      this.Qdistance = 0;
      this.Adistance = 0;
      this.Ddistance = 0;
      this.Wdistance = 0;
      this.Cdistance = 0;
      this.Maxdistance = 0;
      this.loaded = 0;
      this.selected = "0";
      this.text = "";
      for (let i = 0; i < 5; i++) {
        this.$el.getElementsByClassName("prog")[i].style.width = "100%";
      }
    },
    async getSample(version) {
      this.text = "";
      const added = await fetch(`/txt/sample${version}.txt`).then(function (
        response
      ) {
        return response.text();
      });
      this.text += added;
      this.loaded = 2;
      const container = this.$el.querySelector("#textareaid");
      container.scrollTop = container.scrollHeight;
    },
    Format(input) {
      if (this.metric === "Metric") {
        if (input < 1000) {
          return input / 10 + " cm";
        } else if (input < 1000000) {
          return (input / 1000).toFixed(3) + " m";
        } else {
          return (input / 1000000).toFixed(3) + " km";
        }
      } else if (this.metric === "Imperial") {
        if (input < 304.8) {
          return (input * 0.0393701).toFixed(2) + " in";
        } else if (input / 1000 < 1609.34) {
          return (input / 304.8).toFixed(2) + " ft";
        } else {
          return (input / 304.8 / 5280).toFixed(2) + " mi";
        }
      }
    },
  },
};
</script>

<style scoped>
.squaresb {
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='28' height='28' viewBox='0 0 30 30'%3E%3Cg fill-rule='evenodd'%3E%3Cg fill='%233f597c' fill-opacity='0.2' fill-rule='nonzero'%3E%3Cpath d='M6 18h12V6H6v12zM4 4h16v16H4V4z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
}
</style>