<template>
  <div class="tutorial-section">
    <header>
      <h1 class="m-s-sm">
        <!-- The name of the section, displayed in a smaller font above the title -->
        <slot name="name">
          <!-- "Section #"" -->
          <template v-if="!$slots.name && index">
            Section {{ index }}
          </template>
        </slot>
      </h1>
      <h2 class="m-s-med">
        <!-- The title of the section -->
        <slot name="title" />
      </h2>
      <p>
        <!-- The introduction paragraph for the section -->
        <slot name="intro" />
      </p>
    </header>

    <div v-if="$slots.step" class="steps d-flex">
      <div class="step-contents">
        <!-- Add one or more `TutorialStep` components to this slot -->
        <slot name="step" />
      </div>

      <div class="step-assets prism-share-background">
        <transition-group name="fade" tag="div" class="step-assets-inner">
          <tutorial-aside :key="activeStep" :active-step="activeStep" :steps="$slots.step" class="asset-aside-wrapper" />
        </transition-group>
      </div>
    </div>
  </div>
</template>

<script>
import debounce from 'lodash/debounce';
import TutorialAside from '../TutorialAside';

export default {
  name: 'TutorialSection',

  components: {
    TutorialAside
  },

  data() {
    return {
      activeStep: 1,
      index: null
    };
  },

  created() {
    this.handleDebouncedScroll = debounce(this.handleScroll, 15);
    window.addEventListener('scroll', this.handleDebouncedScroll);
  },

  beforeDestroy() {
    window.removeEventListener('scroll', this.handleDebouncedScroll);
  },

  mounted() {
    let index = Array.prototype.indexOf.call(this.$el.parentNode.children, this.$el);
    this.index = index + 1;
  },

  methods: {
    handleScroll() {
      let firstIntersected = this.$el.querySelector('.step-contents .step-intersected');

      if (!firstIntersected) {
        if (window.scrollY > this.$el.offsetTop) {
          // Select last if we are scrolled past all of them
          let size = [...this.$el.querySelectorAll('.step-contents .step-wrapper')].length || 1;
          this.activeStep = size;
        } else {
          this.activeStep = 1;
        }

        return;
      }

      let index = Array.prototype.indexOf.call(firstIntersected.parentNode.children, firstIntersected);
      this.activeStep = index + 1;
    },

    isActiveStep(num) {
      if (this.activeStep === num) return true;
      return false;
    }
  }
};
</script>
