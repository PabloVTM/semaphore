<template>
  <!-- PabloSystems prompt: decorative, the page title says the same to assistive technology. -->
  <span
    class="TerminalPrompt"
    :class="`TerminalPrompt--${variant}`"
    aria-hidden="true"
    data-testid="terminalPrompt"
  ><span class="TerminalPrompt__user">pablo@systems</span
  ><span class="TerminalPrompt__sep">:</span
  ><span class="TerminalPrompt__path">~/semaphore</span
  ><span class="TerminalPrompt__cursor">_</span></span>
</template>

<script>
export default {
  name: 'TerminalPrompt',
  props: {
    // `surface`: on the page or a card, follows the light / dark theme.
    // `drawer`: on the Deep Sage Green navigation drawer.
    variant: {
      type: String,
      default: 'surface',
      validator: (v) => ['surface', 'drawer'].includes(v),
    },
  },
};
</script>

<style lang="scss">
// Text colors keep at least 4.5:1 on their background; the cursor is the raw
// Vibrant Vermillion Red of the theme details.
.TerminalPrompt {
  font-family: var(--v-font-body);
  font-weight: bold;
  white-space: nowrap;
  user-select: none;
}

.TerminalPrompt__cursor {
  color: #f20b0b;
  animation: TerminalPrompt-blink 1.1s steps(1) infinite;
}

@keyframes TerminalPrompt-blink {
  50% {
    opacity: 0;
  }
}

@media (prefers-reduced-motion: reduce) {
  .TerminalPrompt__cursor {
    animation: none;
  }
}

.TerminalPrompt--surface {
  .TerminalPrompt__user {
    color: rgb(var(--v-theme-primary));
  }

  .TerminalPrompt__sep {
    color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));
  }

  .TerminalPrompt__path {
    color: rgb(var(--v-theme-detail-ink));
  }
}

.TerminalPrompt--drawer {
  .TerminalPrompt__user {
    color: #f1eee3;
  }

  .TerminalPrompt__sep {
    color: #d6ddd5;
  }

  .TerminalPrompt__path {
    color: #ffc2bb;
  }
}
</style>
