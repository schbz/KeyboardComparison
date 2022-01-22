<template>
  <div
    class="
      min-h-screen min-w-screen
      bg-gradient-to-b
      from-orange-300
      via-teal-200
      to-teal-200
    "
  >
    <div class="w-full min-h-screen font-mono flex-col flex container mx-auto">
      <div class="h-1/3 w-full p-4 md:flex-row flex flex-none flex-col">
        <div
          class="
            flex
            items-center
            justify-center
            content-center
            mx-auto
            h-24
            md:h-full
            border-black border-4
            shadow-xl
            rounded-md
            max-w-auto
            mb-2
            md:p-4
            sm:mr-6 sm:py-3
            md:py-3
            lg:w-auto
            sm:w-auto sm:min-h-full
            squaresb
            bg-gray-300
          "
        >
          <div
            class="text-3xl sm:text-4xl md:text-5xl ml-6 h-full mt-12 md:mt-4"
          >
            📐
          </div>
          <div class="flex text-center mr-16">
            <h2
              class="
                md:text-3xl
                text-2xl
                sm:text-3xl
                text-left
                leading-none
                font-pacific
              "
            >
              Keyboard Layouts
              <span class="italic md:ml-12 ml-5 mb-2 underline font-pacific"
                >Compared</span
              >
            </h2>
          </div>
        </div>

        <div class="mt-1 items-center justify-center">
          <p class="text-sm sm:text-md xl:text-lg">
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
        class="md:flex-row flex flex-grow flex-col h-2/3 w-full max-w-screen"
      >
        <div class="p-3 w-full md:w-1/2 h-full">
          <textarea
            id="textareaid"
            ref="txt"
            v-model="text"
            class="
              opacity-80
              form-control
              block
              w-full
              px-3
              py-1.5
              text-base text-gray-700
              bg-white bg-clip-padding
              border border-solid border-gray-700
              rounded
              transition
              ease-in-out
              focus:text-gray-700
              focus:opacity-100
              focus:border-blue-800
              focus:outline-none
            "
            rows="5"
            placeholder="Type or paste text here."
          ></textarea>
          <div class="w-full h-auto">
            <span class="float-left text-md"
              ><span class="text-xl">{{ text.length }}</span> Chars
              <span class="text-xl ml-5"> {{ words }}</span> Words
            </span>
            <button
              class="
                float-right
                bg-blue-600
                text-white
                w-20
                h-8
                mt-1
                font-medium
                text-xl
                uppercase
                rounded
                shadow-md
                hover:shadow-lg
                active:bg-blue-700
                hover:bg-blue-800
                transition
                duration-150
                ease-in-out
              "
              type="button"
              :class="{ 'cursor-not-allowed opacity-50': clearDisabled }"
              :disabled="clearDisabled"
              @click="Clear"
            >
              Clear
            </button>
            <div class="clear-both">
              <select v-model="selected" class="bg-white" @change="selecting">
                <option value="0">Load Sample Text</option>
                <option value="1">Declaration of Independence</option>
                <option value="2">This app's source code</option>
                <option value="3">Dr Jekyll and Mr Hyde</option>
              </select>
              <span v-if="loaded != 0">{{
                loaded === 1 ? 'loading...' : 'loaded'
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
                  <label for="one">18 mm </label></span
                >
                <span
                  class="md:font-bold text-xs sm:text-sm md:text-lg"
                  @mouseup="setPitch(19)"
                >
                  <input id="two" v-model="pitch" type="radio" value="2" />
                  <label for="two">19 mm</label></span
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
                    class="md:font-bold text-sm md:text-lg"
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
            <span class="animate-pulse text-3xl">🤯</span>
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
          class="
            md:w-1/2
            max-w-full
            min-h-full
            px-3
            transition-all
            duration-500
            justify-evenly
            gap-5
            py-3
            flex-col flex
            mt-10
            md:mt-1
          "
        >
          <div class="">
            <div class="mb-1 font-bold text-gray-700 text-xl">
              ⌨
              <a
                class="hover:underline"
                href="https://en.wikipedia.org/wiki/QWERTY"
                target="_blank"
                >Qwerty</a
              >
            </div>
            <div class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md">
              <div
                class="
                  bg-gray-600
                  h-full
                  transition-all
                  duration-500
                  text-lg
                  font-medium
                  text-gray-100 text-center
                  p-0.5
                  leading-none
                  prog
                "
                :style="{ width: (Qdistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Qdistance) }}
              </div>
            </div>
          </div>

          <div>
            <div class="mb-1 font-bold text-rose-700 text-xl">
              ⌨
              <a
                class="hover:underline"
                href="https://en.wikipedia.org/wiki/AZERTY"
                target="_blank"
                >Azerty</a
              >
            </div>
            <div class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md">
              <div
                class="
                  bg-rose-600
                  h-full
                  transition-all
                  duration-500
                  text-lg
                  font-medium
                  text-rose-100 text-center
                  p-0.5
                  leading-none
                  prog
                "
                :style="{ width: (Adistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Adistance) }}
              </div>
            </div>
          </div>

          <div>
            <div class="mb-1 font-bold text-indigo-700 text-xl">
              ⌨
              <a
                class="hover:underline"
                href="https://en.wikipedia.org/wiki/Dvorak_keyboard_layout"
                target="_blank"
                >Dvorak</a
              >
            </div>
            <div class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md">
              <div
                class="
                  bg-indigo-600
                  h-full
                  transition-all
                  duration-500
                  text-lg
                  font-medium
                  text-indigo-100 text-center
                  p-0.5
                  leading-none
                  prog
                "
                :style="{ width: (Ddistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Ddistance) }}
              </div>
            </div>
          </div>
          <div>
            <div class="mb-1 font-bold text-emerald-700 text-xl">
              ⌨
              <a
                class="hover:underline"
                href="https://en.wikipedia.org/wiki/Colemak"
                target="_blank"
                >Colemak</a
              >
            </div>
            <div class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md">
              <div
                class="
                  bg-emerald-600
                  h-full
                  transition-all
                  duration-500
                  text-lg
                  font-medium
                  text-emerald-100 text-center
                  p-0.5
                  leading-none
                  prog
                "
                :style="{ width: (Cdistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Cdistance) }}
              </div>
            </div>
          </div>

          <div class="mb-5">
            <div class="mb-1 font-bold text-amber-700 text-xl">
              ⌨
              <a
                class="hover:underline"
                href="https://workmanlayout.org/"
                target="_blank"
                >Workman</a
              >
            </div>
            <div class="w-full bg-gray-200 rounded-r-full h-6 overflow-hidden shadow-md">
              <div
                class="
                  bg-amber-600
                  h-full
                  transition-all
                  duration-500
                  text-lg
                  font-medium
                  text-amber-100 text-center
                  p-0.5
                  leading-none
                  prog
                "
                :style="{ width: (Wdistance / Maxdistance) * 97 + '%' }"
              >
                {{ Format(Wdistance) }}
              </div>
            </div>
          </div>
        </div>
      </div>

      <div
        class="
          w-full
          h-auto
          mt-4
          pb-5
          justify-center
          font-mono
          flex
          text-sm
          sm:text-md
        "
      >
        <div class="text-center">
          <div class="flex flex-col sm:flex-row gap-1">
            <div><span>Created using Dvorak layout </span><br /></div>
            <div class="flex-row items-center">
              <span class="ml-1"
                >©{{ new Date().getFullYear() }}
                <a href="https://slsc.link/app">Schuyler Bezley</a></span
              ><a
                href="https://github.com/schbz/KeyboardComparison"
                class="float-right ml-1"
                ><svg
                  class="-mt-1"
                  xmlns="http://www.w3.org/2000/svg"
                  x="0px"
                  y="0px"
                  width="28"
                  height="28"
                  viewBox="0 0 24 24"
                  style="fill: #000000"
                >
                  <path
                    d="M10.9,2.1c-4.6,0.5-8.3,4.2-8.8,8.7c-0.5,4.7,2.2,8.9,6.3,10.5C8.7,21.4,9,21.2,9,20.8v-1.6c0,0-0.4,0.1-0.9,0.1 c-1.4,0-2-1.2-2.1-1.9c-0.1-0.4-0.3-0.7-0.6-1C5.1,16.3,5,16.3,5,16.2C5,16,5.3,16,5.4,16c0.6,0,1.1,0.7,1.3,1c0.5,0.8,1.1,1,1.4,1 c0.4,0,0.7-0.1,0.9-0.2c0.1-0.7,0.4-1.4,1-1.8c-2.3-0.5-4-1.8-4-4c0-1.1,0.5-2.2,1.2-3C7.1,8.8,7,8.3,7,7.6c0-0.4,0-0.9,0.2-1.3 C7.2,6.1,7.4,6,7.5,6c0,0,0.1,0,0.1,0C8.1,6.1,9.1,6.4,10,7.3C10.6,7.1,11.3,7,12,7s1.4,0.1,2,0.3c0.9-0.9,2-1.2,2.5-1.3 c0,0,0.1,0,0.1,0c0.2,0,0.3,0.1,0.4,0.3C17,6.7,17,7.2,17,7.6c0,0.8-0.1,1.2-0.2,1.4c0.7,0.8,1.2,1.8,1.2,3c0,2.2-1.7,3.5-4,4 c0.6,0.5,1,1.4,1,2.3v2.6c0,0.3,0.3,0.6,0.7,0.5c3.7-1.5,6.3-5.1,6.3-9.3C22,6.1,16.9,1.4,10.9,2.1z"
                  ></path></svg
              ></a>
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
      text: '',
      words: 0,
      clearDisabled: true,
      Qhomerow: ['a', 's', 'd', 'f', 'j', 'k', 'l', ';'],
      Ahomerow: ['q', 's', 'd', 'f', 'j', 'k', 'l', 'm'],
      Dhomerow: ['a', 'o', 'e', 'u', 'h', 't', 'n', 's'],
      Chomerow: ['a', 'r', 's', 't', 'n', 'e', 'i', 'o'],
      Whomerow: ['a', 's', 'h', 't', 'n', 'e', 'o', 'i'],
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
      sample: ' default',
      selected: 0,
      loaded: 0,
      pitch: 1,
      metric: 'Metric',
    }
  },
  computed: {
    serious() {
      return this.Maxdistance - this.Mindistance > 300
    },
  },
  watch: {
    text(newText, oldText) {
      this.Count()
    },
  },
  methods: {
    setPitch(num) {
      this.avgdistmm = num
      this.Count()
    },
    selecting() {
      if (this.selected !== '0') {
        this.loaded = 1

        this.fetchSample(this.selected)
      }
    },
    Count() {
      this.Qdistance = 0
      this.Adistance = 0
      this.Ddistance = 0
      this.Cdistance = 0
      this.Wdistance = 0
      this.words = 0

      for (let i = 0; i <= this.text.length; i++) {
        if (this.text.charAt(i) === ' ' && this.text.charAt(i - 1) !== ' ') {
          this.words++
          this.Qdistance += this.spacebardist * 2
          this.Adistance += this.spacebardist * 2
          this.Ddistance += this.spacebardist * 2
          this.Cdistance += this.spacebardist * 2
          this.Wdistance += this.spacebardist * 2
        } else if (
          this.text.charAt(i) === ' ' &&
          this.text.charAt(i - 1) === ' '
        ) {
          this.Qdistance += this.spacebardist * 2
          this.Adistance += this.spacebardist * 2
          this.Ddistance += this.spacebardist * 2
          this.Cdistance += this.spacebardist * 2
          this.Wdistance += this.spacebardist * 2
        } else {
          if (!this.Qhomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Qdistance += this.avgdistmm * 2
            }
          } else {
            this.Qdistance += this.homerowdist * 2
          }
          if (!this.Ahomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Adistance += this.avgdistmm * 2
            }
          } else {
            this.Adistance += this.homerowdist * 2
          }
          if (!this.Dhomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Ddistance += this.avgdistmm * 2
            }
          } else {
            this.Ddistance += this.homerowdist * 2
          }
          if (!this.Chomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Cdistance += this.avgdistmm * 2
            }
          } else {
            this.Cdistance += this.homerowdist * 2
          }
          if (!this.Whomerow.includes(this.text.charAt(i).toLowerCase())) {
            if (this.text.charAt(i) !== this.text.charAt(i - 1)) {
              this.Wdistance += this.avgdistmm * 2
            }
          } else {
            this.Wdistance += this.homerowdist * 2
          }
        }
      }
      this.totaldistance =
        this.Qdistance +
        this.Ddistance +
        this.Cdistance +
        this.Wdistance +
        this.Adistance

      if (this.text !== '') {
        this.clearDisabled = false
      } else {
        this.clearDisabled = true
      }

      this.Maxdistance = Math.max(
        this.Qdistance,
        this.Adistance,
        this.Ddistance,
        this.Cdistance,
        this.Wdistance
      )
      this.Mindistance = Math.min(
        this.Qdistance,
        this.Adistance,
        this.Ddistance,
        this.Cdistance,
        this.Wdistance
      )
    },

    Clear() {
      this.clearDisabled = true
      this.words = 0
      this.Qdistance = 0
      this.Adistance = 0
      this.Ddistance = 0
      this.Wdistance = 0
      this.Cdistance = 0
      this.Maxdistance = 0
      this.loaded = 0
      this.selected = '0'
      this.text = ''
      for (let i = 0; i < 5; i++) {
        this.$el.getElementsByClassName('prog')[i].style.width = '100%'
      }
    },
    async fetchSample(version) {
      await this.$axios
        .$get(`/txt/sample${version}.txt`)
        .then((response) => {
          this.text += response
        })
        .catch((error) => {
          alert(error)
        })
      this.loaded = 2
      const container = this.$el.querySelector('#textareaid')
      container.scrollTop = container.scrollHeight
    },
    Format(input) {
      if (this.metric === 'Metric') {
        if (input < 1000) {
          return input / 10 + ' cm'
        } else if (input < 1000000) {
          return (input / 1000).toFixed(3) + ' m'
        } else {
          return (input / 1000000).toFixed(3) + ' km'
        }
      } else if (this.metric === 'Imperial') {
        if (input < 304.8) {
          return (input * 0.0393701).toFixed(2) + ' in'
        } else if (input / 1000 < 1609.34) {
          return (input / 304.8).toFixed(2) + ' ft'
        } else {
          return (input / 304.8 / 5280).toFixed(2) + ' mi'
        }
      }
    },
  },
}
</script>

<style scoped>
.squaresb {
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='28' height='28' viewBox='0 0 30 30'%3E%3Cg fill-rule='evenodd'%3E%3Cg fill='%233f597c' fill-opacity='0.2' fill-rule='nonzero'%3E%3Cpath d='M6 18h12V6H6v12zM4 4h16v16H4V4z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
}
</style>
