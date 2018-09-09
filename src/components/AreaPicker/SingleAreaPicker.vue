<template>
  <div class="single-area-picker">
    <div class="area-picker-wrapper">
      <div ref="apSelectResult" :class="['ap-select-result', {'is-focus': isFocus}]" @click="toggleOptionPanel">
        <input class="ap-input" type="text" readonly="true" :value="selectedValue" placeholder="请选择 “省份 / 城市 / 地区”">
        <i :class="['ap-input-icon', 'iconfont', {'icon-jiantouxia' : !optionPanelStatus, 'icon-jiantoushang': optionPanelStatus}]"></i>
      </div>
      <transition name="fade">
        <div
          ref="apOptionPanel"
          v-show="optionPanelStatus"
          class="ap-option-panel"
          :style="optionPanelPosition">
          <div class="option-panel-header">
            <div class="option-panel-title">{{ panelTitle }}</div>
            <i class="close-icon iconfont icon-close-circle" @click="closeOptionPanel"></i>
          </div>
          <div class="option-panel-body">
            <div v-show="provinceStatus" @click="selectProvince" class="province-table ap-table">
              <template v-for="(item, index) in province">
                <div :key="index" :province-id="item.id" class="province ap-table-cell"><span>{{ item.name }}</span></div>
              </template>
            </div>
            <div v-if="cityStatus" @click="selectCity" class="city-table ap-table">
              <template v-for="(item, index) in city">
                <div :key="index" :city-id="item.id" class="city ap-table-cell"><span>{{ item.name }}</span></div>
              </template>
            </div>
            <div v-if="areaStatus" @click="selectArea" class="area-table ap-table">
              <template v-for="(item, index) in area">
                <div :key="index" :area-id="item.id" class="area ap-table-cell"><span>{{ item.name }}</span></div>
              </template>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
import AREA_INFO from "./area-info.js";

export default {
  name: "single-area-picker",
  data() {
    return {
      optionPanelStatus: false,
      provinceStatus: false,
      cityStatus: false,
      areaStatus: false,
      panelTitle: "",
      province: [],
      city: [],
      area: [],
      selectedInfo: {
        province: "",
        city: "",
        area: ""
      },
      isFocus: false,
      optionPanelPosition: {}
    };
  },
  computed: {
    selectedValue() {
      let val = "";
      for (let i in this.selectedInfo) {
        if (this.selectedInfo[i]) {
          if (i === "province") {
            val += this.selectedInfo[i];
          } else {
            val += ` / ${this.selectedInfo[i]}`;
          }
        }
      }
      return val;
    }
  },
  mounted() {},
  methods: {
    toggleOptionPanel() {
      this.optionPanelStatus = !this.optionPanelStatus;
      this.isFocus = !this.isFocus;
      if (this.optionPanelStatus) {
        document.body.appendChild(this.$refs.apOptionPanel);
        this.setOptionPanelPosition();
        this.showProvince();
      } else {
        this.closeOptionPanel();
      }
    },
    setOptionPanelPosition() {
      let position = {
        top: '',
        left: '',
      };
      if(this.$refs.apSelectResult) {
        let apSelectResultPosition = this.$refs.apSelectResult.getBoundingClientRect();
        position.top = apSelectResultPosition.top + apSelectResultPosition.height;
        position.left = apSelectResultPosition.left;
      }
      this.optionPanelPosition.top = `${position.top}px`;
      this.optionPanelPosition.left = `${position.left}px`;
    },
    closeOptionPanel() {
      this.optionPanelStatus = false;
      if (this.cityStatus) this.cityStatus = false;
      if (this.areaStatus) this.areaStatus = false;
      console.log(this.selectedInfo);
    },
    showProvince() {
      this.provinceStatus = true;
      if (this.optionPanelStatus && this.province.length === 0) {
        for (let i = 0, len = AREA_INFO.length; i < len; i++) {
          this.province.push({
            id: AREA_INFO[i].id,
            name: AREA_INFO[i].name
          });
        }
        // console.log(this.province);
      }
      this.panelTitle = `请选择省份`;
    },
    selectProvince(e) {
      this.selectedInfo = {
        province: "",
        city: "",
        area: ""
      };
      let target = e.target;
      if (target.nodeName.toLowerCase() === "span") {
        this.selectedInfo.province = target.innerText;
        this.showCity(target);
      }
    },
    showCity(target) {
      this.provinceStatus = false;
      this.cityStatus = true;
      for (let i = 0, len = AREA_INFO.length; i < len; i++) {
        if (AREA_INFO[i].name === target.innerText) {
          this.city = AREA_INFO[i].children;
          break;
        }
      }
      // console.log(this.city);
      this.panelTitle = `请选择城市`;
    },
    selectCity(e) {
      let target = e.target;
      if (target.nodeName.toLowerCase() === "span") {
        this.selectedInfo.city = target.innerText;
        if (this.city[0].children) {
          this.showArea(target);
        } else {
          this.closeOptionPanel();
        }
      }
    },
    showArea(target) {
      this.cityStatus = false;
      this.areaStatus = true;
      for (let i = 0, len = this.city.length; i < len; i++) {
        if (this.city[i].name === target.innerText) {
          this.area = this.city[i].children;
          break;
        }
      }
      // console.log(this.area);
      this.panelTitle = `请选择区县`;
    },
    selectArea(e) {
      let target = e.target;
      if (target.nodeName.toLowerCase() === "span") {
        this.selectedInfo.area = target.innerText;
        this.closeOptionPanel();
      }
    }
  }
};
</script>

<style scoped>
.single-area-picker {
  font-size: 12px;

  & .ap-select-result {
    position: relative;
    width: 500px;

    &.is-focus {
      & .ap-input {
        border-color: #03a9f4c4;
      }
    }
  }

  & .ap-input {
    box-sizing: border-box;
    width: 100%;
    height: 32px;
    padding: 0 8px;
    border: 1px solid #9e9e9e7a;
    border-radius: 4px;
    cursor: pointer;

    &:focus {
      border-color: #03a9f4c4;
    }
  }

  & .ap-input-icon {
    position: absolute;
    top: 50%;
    right: 8px;
    transform: translateY(-50%);
    color: #9e9e9e;
    cursor: pointer;
  }
}

.ap-option-panel {
  position: absolute;
  top: 0;
  z-index: 1000;
  box-sizing: border-box;
  width: 500px;
  padding: 10px 20px;
  border: 1px solid #9e9e9e7a;
  border-radius: 4px;
  margin-top: 8px;
  box-shadow: 0 2px 4px #9e9e9e7a;
  font-size: 12px;
  background: #fff;

  & .option-panel-header {
    height: 30px;
    border-bottom: 1px solid #9e9e9e7a;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  & .close-icon {
    cursor: pointer;
  }

  & .option-panel-body {
    box-sizing: border-box;
    padding: 10px 0 0;
  }

  & .ap-table {
    text-align: left;
  }

  & .ap-table-cell {
    display: inline-block;
    width: calc(100% / 3);
    height: 24px;
    margin-bottom: 8px;
    text-align: left;
    line-height: 24px;

    & > span {
      display: inline-block;
      padding: 0 8px;
      border-radius: 4px;
      cursor: pointer;

      &:hover {
        color: #03a9f4;
        background: #e0d7d763;
      }
    }
  }
}
</style>