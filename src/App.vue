<script setup>
import { ref } from "vue";
import { getBrowserData } from "/src/assets/js/getBrowserData.js";

let height = ref(false);
let width = ref(false);
let screenWidth = ref(false);
let screenHeight = ref(false);
let colorDepth = ref(false);
let pixelDepth = ref(false);
let browserAgent = ref(false);
let browserName = ref(false);
let browserVersion = ref(false);
let browserMajorVersion = ref(false);

let colors = ["#f4796b", "#643A71", "#32908F", "#FE7F2D"];

const text = ref(null);
const root = ref(null);

const getData = () => {
  let data = getBrowserData();
  browserName.value = data[0];
  browserVersion.value = data[1];
  browserMajorVersion.value = data[2];
  browserAgent.value = data[3];
  height.value = window.innerHeight;
  width.value = window.innerWidth;
  screenWidth.value = screen.width;
  screenHeight.value = screen.height;
  colorDepth.value = screen.colorDepth;
  pixelDepth.value = screen.pixelDepth;
};

const copyToClipBoard = () => {
  var textArea = document.createElement("textarea");
  textArea.value =
    "My browser information: " +
    text.value.innerHTML.replace(/<\/p>/g, "; ").replace(/(<([^>]+)>)/gi, "");

  document.body.appendChild(textArea);
  textArea.focus();
  textArea.select();
  try {
    var successful = document.execCommand("copy");
    var msg = successful ? "successful" : "unsuccessful";
    console.log("Copying text command was " + msg);
  } catch (err) {
    console.log("Oops, unable to copy");
  }
};

const changeBackground = (color) => {
  if (color == "default") {
    root.value.style.removeProperty("--color-accent");
  } else root.value.style.setProperty("--color-accent", color);
};

const fixChangeBackground = (color) => {
  document.documentElement.style.setProperty("--color-accent", color);
};

getData();
window.addEventListener("resize", getData);
</script>

<template>
  <div class="browserInfo" ref="root">
    <div class="browserInfo__icon">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        width="24"
        height="24"
        viewBox="0 0 24 24"
      >
        <path
          d="M0 0v19h24v-19h-24zm22 17h-20v-15h20v15zm-6.599 4l2.599 3h-12l2.599-3h6.802zm-6.401-16l6 4.674-2.538.427 1.538 3.095-1.571.804-1.546-3.157-1.883 1.759v-7.602z"
        />
      </svg>
    </div>

    <h1>What's my browser info?</h1>
    <p class="browserInfo__copy text--center">
      Browser and screen information to share with your support team.
    </p>

    <ul>
      <li
        v-for="color in colors"
        :style="{ '--bg-color': color }"
        @mouseover="changeBackground(color)"
        @mouseleave="changeBackground('default')"
        @click="fixChangeBackground(color)"
      ></li>
    </ul>

    <div class="browserInfo__copyright">
      <p>
        Created by Nadezda G,
        <a
          href="https://github.com/NadezdaG/browserinfo"
          title="small VUE app for browser information detection"
          >GitHub</a
        >
      </p>
    </div>

    <main class="browserInfo__main">
      <div class="browserInfo__text" ref="text">
        <p v-if="browserName"><strong>Browser:</strong>{{ browserName }}</p>
        <p v-if="browserMajorVersion">
          <strong>Browser Version:</strong> {{ browserMajorVersion }} ({{
            browserVersion
          }})
        </p>
        <p><strong>User Agent:</strong> {{ browserAgent }}</p>
        <p v-if="screenWidth">
          <strong>Screen Size:</strong> {{ screenWidth }}/{{ screenHeight }}
        </p>
        <p v-if="width">
          <strong>Window Size:</strong> {{ width }}/{{ height }}
        </p>
        <p v-if="colorDepth"><strong>Color Depth:</strong> {{ colorDepth }}</p>
        <p v-if="pixelDepth"><strong>Pixel Depth:</strong> {{ pixelDepth }}</p>
      </div>

      <button @click="copyToClipBoard()">
        Copy
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          viewBox="0 0 24 24"
        >
          <path
            d="M10 8h-2v2h-2v-4h4v2zm8 16v-2h-6v2h6zm2-16h2v2h2v-4h-4v2zm2 12v2h-2v2h4v-4h-2zm-12 2h-2v-2h-2v4h4v-2zm14-10h-2v6h2v-6zm-16 6v-6h-2v4h-4v-14h14v4h-4v2h6v-8h-18v18h8z"
          />
        </svg>
      </button>
    </main>
  </div>
</template>

<style lang="scss">
@use "./assets/scss/reset.scss";
@use "./assets/scss/variables.scss";
@use "./assets/scss/global.scss";
@use "./assets/scss/fonts.scss";

.browserInfo {
  --local-padding: var(--padding-big-mobile);
  @media (min-width: 768px) {
    --local-padding: var(--padding-big);
  }
  max-width: 1280px;
  margin: 0 auto;
  padding: var(--local-padding);
  display: grid;
  min-height: 100vh;
  grid-template-columns: 1fr;
  grid-template-rows: auto auto auto auto auto;
  grid-template-areas: "icon" "title" "copy" "color" "content" "copyright";
  @media (min-width: 768px) {
    grid-template-columns: 1fr 2fr;
    grid-template-rows: auto auto auto auto 1fr;
    grid-template-areas: "icon content" "title content" "copy content" "color content" "copyright content";
  }

  ul {
    grid-area: color;
    margin: 0;
    padding: 0;
    list-style: none;
    display: flex;
    flex-wrap: nowrap;
    justify-content: stretch;
    margin-top: 1em;
    li {
      background-color: var(--bg-color);
      height: 30px;
      flex-grow: 1;
      width: 33%;
      cursor: pointer;
    }
  }

  &__icon {
    grid-area: icon;
    svg {
      width: 100%;
      height: auto;
      max-width: 400px;
    }
    path {
      fill: var(--color-accent);
    }
  }
  h1 {
    grid-area: title;
    margin-top: 1em;
    color: var(--color-accent);
  }
  &__copy {
    grid-area: copy;
    font-size: 0.8em;
  }
  &__copyright {
    grid-area: copyright;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
    font-size: 0.6em;
  }

  &__main {
    --local-padding: var(--padding-mobile);
    @media (min-width: 768px) {
      --local-padding: var(--padding);
    }
    grid-area: content;
    margin-left: 0;
    padding: var(--local-padding);
    background-color: var(--color-accent);
    color: var(--color-white);

    align-items: flex-start;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    margin-top: 1em;
    margin-bottom: 1em;
    @media (min-width: 768px) {
      margin-left: var(--local-padding);
      margin-top: 0;
      margin-bottom: 0;
    }
  }

  &__text {
    p {
      display: grid;
      grid-template-columns: 1fr 2fr;
      margin-bottom: 0.5em;
      &:last-child {
        margin-bottom: 0;
      }
    }
  }

  button {
    display: flex;
    justify-align: center;
    align-items: center;
    font-size: 15px;
    text-transform: uppercase;
    border: none;
    padding: 0.5em 2em;
    font-weight: 600;
    align-self: flex-end;
    cursor: pointer;

    svg {
      height: 15px;
      width: auto;
      margin-left: 10px;
    }
    &__hover {
      opacity: 0.5;
    }
  }
}

textarea {
  width: 0;
  height: 0;
  opacity: 0;
}
</style>
