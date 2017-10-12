<template>
  <button
    v-ripple.mat="!isDisabled"
    @click="click"
    class="q-btn row inline flex-center q-focusable q-hoverable relative-position"
    :class="classes"
  >
    <div class="desktop-only q-focus-helper"></div>
    <div
      v-if="loading && hasPercentage"
      class="q-btn-progress absolute-full"
      :class="{'q-btn-dark-progress': darkPercentage}"
      :style="{width: width}"
    ></div>

    <span
      class="q-btn-inner row col flex-center"
      :class="{'no-wrap': noWrap, 'text-no-wrap': noWrap}"
    >
      <slot v-if="loading" name="loading">
        <q-spinner></q-spinner>
      </slot>

      <template v-else>
        <q-icon v-if="icon" :name="icon" :class="{'on-left': label && !round}"></q-icon>
        <span v-if="label && !round">{{ label }}</span>
        <span><slot></slot></span>
        <q-icon v-if="!round && iconRight" :name="iconRight" class="on-right"></q-icon>
      </template>
    </span>
  </button>
</template>

<script>
import { format } from 'quasar'

var between = format.between

export default {
  name: 'slottest',
  props: {
    value: Boolean,
    loader: Boolean,
    percentage: Number,
    darkPercentage: Boolean,
    disable: Boolean,
    label: String,
    noCaps: Boolean,
    noWrap: Boolean,
    icon: String,
    iconRight: String,
    round: Boolean,
    outline: Boolean,
    flat: Boolean,
    rounded: Boolean,
    push: Boolean,
    small: Boolean,
    big: Boolean,
    color: String,
    glossy: Boolean,
    compact: Boolean
  },
  data () {
    return {
      loading: this.value || false
    }
  },
  watch: {
    value (val) {
      if (this.loading !== val) {
        this.loading = val
      }
    }
  },
  computed: {
    hasPercentage () {
      return this.percentage !== void 0
    },
    width () {
      return `${between(this.percentage, 0, 100)}%`
    },
    size () {
      return `q-btn-${this.small ? 'small' : (this.big ? 'big' : 'standard')}`
    },
    shape () {
      return `q-btn-${this.round ? 'round' : 'rectangle'}`
    },
    isDisabled () {
      return this.disable || this.loading
    },
    classes () {
      const
        cls = [this.shape, this.size],
        color = this.toggled ? this.toggleColor : this.color

      if (this.toggled) {
        cls.push('q-btn-toggle-active')
      }
      if (this.compact) {
        cls.push('q-btn-compact')
      }

      if (this.flat) {
        cls.push('q-btn-flat')
      }
      else if (this.outline) {
        cls.push('q-btn-outline')
      }
      else if (this.push) {
        cls.push('q-btn-push')
      }

      this.isDisabled && cls.push('disabled')
      this.noCaps && cls.push('q-btn-no-uppercase')
      this.rounded && cls.push('q-btn-rounded')
      this.glossy && cls.push('glossy')

      if (color) {
        if (this.flat || this.outline) {
          cls.push(`text-${color}`)
        }
        else {
          cls.push(`bg-${color}`)
          cls.push(`text-white`)
        }
      }

      return cls
    }
  },
  methods: {
    click (e) {
      this.$el.blur()

      if (this.isDisabled) {
        return
      }
      if (this.loader !== false || this.$slots.loading) {
        this.loading = true
        this.$emit('input', true)
      }
      this.$emit('click', e, () => {
        this.loading = false
        this.$emit('input', false)
      })
    }
  }
}
</script>
