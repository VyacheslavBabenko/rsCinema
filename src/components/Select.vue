<template>
  <div
    class="custom-select"
    :tabindex="tabindex"
    @blur="open = false"
  >
    <div
      class="selected"
      @click="open = !open"
    >
      {{ selected }}
    </div>
    <div
      class="items"
      :class="{selectHide: !open}"
    >
    <template v-if="typeof this.options[0] == 'object'">
      <div
        class="item"
        v-for="(option, i) of options"
        :key="option.id"
        :class="{active: checkActive(i)}"
        @click="sendOption(option.title, i, option.id)"
      >
        {{ option.title }}
      </div>
    </template>
    <template v-else>
       <div
        class="item"
        v-for="(option, i) of options"
        :key="i"
        :class="{active: checkActive(i)}"
        @click="sendOption(option, i)"
      >
        {{ option }}
      </div>
    </template>
    </div>
  </div>
</template>

<script>
export default {
  props:{
    options: {
      required: true,
      default: ''
    },
    tabindex: {
      type: Number,
      required: false,
      default: 0
    },
    defaultOptions: {
      type: String,
      required: true
    }, 
  },
  data() {
    return {
      selected:  this.defaultOptions,
      open: false,
      isActive: null,
    };
  },
  methods: {
    sendOption(option, i, optionId = -1) {
      this.isActive = i;
      this.selected = option; 
      this.open = false;

      if (optionId != -1) {
        this.$emit('input', optionId);
      } else {
        this.$emit('input', this.selected);
      }
      
    },
    checkActive(i) {
      if (this.isActive == i) {
        return true;
      }
    },
  }
};
</script>


<style lang="scss" scoped>

.custom-select {
  position: relative;
  outline: none;
  display: flex;
  align-items: center;
  color: #979797;
  width: 192px;

  @media (max-width: 1200px) {
    width: 100%;
    background: url('../assets/Chevron-bottom.svg') 95% no-repeat #FFFFFF;
  }
}



.selected {
  background: url('../assets/Chevron-bottom.svg') 89% no-repeat #FFFFFF;
  width: 100%;
  font-weight: normal;
  font-size: 16px;
  line-height: 24px;
  padding-left: 20px;
  cursor: pointer;
  user-select: none;

  &::first-letter {
    text-transform: uppercase;
  }

  @media (max-width: 1200px) {
    padding-left: 0px;
    width: 100%;
    background: url('../assets/Chevron-bottom.svg') 95% no-repeat #FFFFFF;
  }
}

.items {
  font-weight: normal;
  font-size: 14px;
  line-height: 20px;
  border-radius: 0px 0px 6px 6px;
  overflow: hidden;
  box-shadow: 0px 8px 24px rgba(53, 53, 53, 0.16);
  position: absolute;
  background-color: #FFFFFF;
  left: 0;
  top: 64px;
  z-index: -1;
  width: 192px;
  height: auto;
  padding-bottom: 14px;

  @media (max-width: 1200px) {
    z-index: 1;
    width: 100%;
  }
}

.item{
  color: #979797;
  cursor: pointer;
  user-select: none;
  margin: 16px 0px 0px 20px;

  &::first-letter {
    text-transform: uppercase;
  }
}

.item:hover{
  color: #353535;
}

.active {
  background: url('../assets/checked_select.svg') 89% no-repeat #FFFFFF;
  color: #353535;
}

.selectHide {
  display: none;
}
</style>