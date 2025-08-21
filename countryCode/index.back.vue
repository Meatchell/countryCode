<template>
  <div class="intl-tel-input allow-dropdown">
    <div class="flag-container">
      <div class="selected-flag" :title="currentData.name + ': +' + currentData.dialCode">
        <!-- <div :class="'iti-flag ' + currentData.code"></div> -->
        <div class="code country-show" @click="hideSubMenu = !hideSubMenu"
        >+{{ currentAreaCode }}</div
        >
      </div>
      <ul class="country-list" v-show="!hideSubMenu">
        <!-- <li
          class="country-show"
          v-for="item in frontCountriesArray"
          :key="item.id"
          :class="'country ' + (item.code == currentCode ? 'highlight' : '')"
          @click="currentCode = item.code; hideSubMenu = true; setCountry(item);"
        >
          <div class="flag-box">
            <div :class="'iti-flag ' + item.code"></div>
          </div>
          <span class="country-name">{{ item.name }}</span>
          <span class="dial-code">+{{ item.dialCode }}</span>
        </li>
        <li class="divider"></li> -->
        <li
          class="country-show"
          v-for="item in countriesArray"
          :key="item.id"
          :class="'country ' + (item.code == currentCode ? 'highlight' : '')"
          @click="handleCountryClick(item)"
        >
          <div class="flag-box">
            <div :class="'iti-flag ' + item.code"></div>
          </div>
          <span class="country-name">{{ item.name }}</span>
          <span class="dial-code">+{{ item.dialCode }}</span>
        </li>
      </ul>
    </div>
    <div class="iti-arrow"></div>
  </div>
</template>

<script>
// import { mapState } from 'vuex'
import countries from './countryList'

export default {
  props: {
    toFront: {
      type: Array,
      default: () => [],
    },
    countryCode: {
      type: String,
      default: Object.keys(countries)[0],
      // validator(code) {
      //   const clearCode = String(code).toLowerCase()
      //   return !!countries[clearCode]
      // },
    },
  },

  data() {
    return {
      currentCode: this.countryCode,
      currentAreaCode: '',
      hideSubMenu: true,
    }
  },

  computed: {
    // ...mapState({
    //   webConfig: state => state.webConfig,
    // }),
    currentData() {
      // return countries[this.currentCode]
      const current = Object.values(countries).filter(
        country => country.dialCode === +this.countryCode,
      )[0]
      return current
    },
    frontCountriesArray() {
      const toFrontCodes = {}
      this.toFront.forEach(code => {
        const stringCode = String(code)
        const needObj = countries[stringCode]

        if (needObj) {
          toFrontCodes[stringCode] = needObj
        }
      })
      return toFrontCodes
    },
    countriesArray() {
      const countryCopie = { ...countries }
      this.toFront.forEach(code => {
        delete countryCopie[code]
      })

      console.log(countryCopie.length, '--')
      return countryCopie
    },
  },

  watch: {
    countryCode(newCode) {
      this.setCountry(countries[newCode])
    },
  },

  methods: {
    setCountry(item) {
      if (!item) return
      this.currentCode = item.code
      this.currentAreaCode = item.dialCode
      // this.toFront.push(String(item.code))
      this.$emit('excountry', item)
    },
    handleCountryClick(item) {
      this.currentCode = item.code
      this.hideSubMenu = true
      this.setCountry(item)
    },
  },

  mounted() {
    // init
    // this.currentAreaCode = this.webConfig.defaultMobileCountryNum || '86'
    this.currentAreaCode = '86'
    this.$emit('excountry', countries[this.countryCode])
    const that = this
    try {
      document.addEventListener(
        'click',
        params => {
          const { srcElement, toElement } = params
          const clickDomClassName = `${srcElement?.className} ${toElement?.className}`
          if (!clickDomClassName.includes('country-show')) {
            that.$nextTick(() => {
              that.hideSubMenu = true
            })
          }
        },
        false,
      )
    } catch (message) {
      this.$notify({
        showClose: true,
        type: 'error',
        message,
      })
    }
  },
  beforeDestroy() {
  },
}
</script>

<style lang="scss" scoped>
@import 'intl.css';

.intl-tel-input {
  width: 100%;
  height: 32px;
  color: #666;
  font-size: 14px;
  .flag-container {
    .code {
      height: 32px;
      line-height: 32px;
      margin: 10px 0 1rem;
      border-bottom: 1px solid #dcdfe6;
      text-indent: 20px;
    }
  }
  .country-list {
    width: 300px;
    height: 230px;
    margin-top: 2px;
  }
}
</style>
