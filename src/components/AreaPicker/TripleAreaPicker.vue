<template>
  <div class="triple-area-picker">
    <div class="area-picker-wrapper">
      <div class="province">
        <div
          ref="apSelectResult-province"
          class="ap-select-result"
          @click="toggleOptionPanel('province')">
          <input class="ap-input" type="text" readonly="true" :value="selectedInfo.province" placeholder="请选择省份">
          <i :class="['ap-input-icon', 'iconfont', {'icon-jiantouxia' : !optionPanelStatus.province, 'icon-jiantoushang': optionPanelStatus}]"></i>
        </div>
        <transition name="fade">
          <div
            ref="apOptionPanel-province"
            v-show="optionPanelStatus.province"
            class="ap-option-panel"
            :style="optionPanelPosition.province">
            <div class="option-panel-body">
              <div class="province-table ap-table" @click="selectProvince">
                <template v-for="(item, index) in province">
                  <div :key="index" :province-id="item.id" class="ap-table-cell"><span>{{ item.name }}</span></div>
                </template>
              </div>
            </div>
          </div>
        </transition>
      </div>
      <div class="city">
        <div
          ref="apSelectResult-city"
          class="ap-select-result"
          :title="!cityFlag ? '请先选择省份' : ''"
          @click="cityFlag && toggleOptionPanel('city')">
          <input
            :class="['ap-input', {'disabled': !cityFlag}]"
            type="text"
            readonly="true"
            :disabled="!cityFlag"
            :value="selectedInfo.city"
            placeholder="请选择城市">
          <i :class="['ap-input-icon', 'iconfont', {'icon-jiantouxia' : !optionPanelStatus.city, 'icon-jiantoushang': optionPanelStatus, 'disabled': !cityFlag}]"></i>
        </div>
        <transition name="fade">
          <div
            ref="apOptionPanel-city"
            v-show="optionPanelStatus.city"
            class="ap-option-panel"
            :style="optionPanelPosition.city">
            <div class="option-panel-body">
              <div class="province-table ap-table" @click="selectCity">
                <template v-for="(item, index) in city">
                  <div :key="index" :city-id="item.id" class="ap-table-cell"><span>{{ item.name }}</span></div>
                </template>
              </div>
            </div>
          </div>
        </transition>
      </div>
      <div v-if="hasArea" class="area">
        <div
          ref="apSelectResult-area"
          class="ap-select-result"
          :title="!areaFlag ? '请先选择省份或城市' : ''"
          @click="areaFlag && toggleOptionPanel('area')">
          <input
            :class="['ap-input', {'disabled': !areaFlag}]"
            type="text"
            readonly="true"
            :disabled="!areaFlag" 
            :value="selectedInfo.area" 
            placeholder="请选择地区">
          <i :class="['ap-input-icon', 'iconfont', {'icon-jiantouxia' : !optionPanelStatus.area, 'icon-jiantoushang': optionPanelStatus, 'disabled': !cityFlag}]"></i>
        </div>
        <transition name="fade">
          <div
            ref="apOptionPanel-area"
            v-show="optionPanelStatus.area"
            class="ap-option-panel"
            :style="optionPanelPosition.area">
            <div class="option-panel-body">
              <div class="province-table ap-table" @click="selectArea">
                <template v-for="(item, index) in area">
                  <div :key="index" :area-id="item.id" class="ap-table-cell"><span>{{ item.name }}</span></div>
                </template>
              </div>
            </div>
          </div>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import AREA_INFO from "./area-info.js";

export default {
  name: "triple-area-picker",
  data() {
    return {
      optionPanelStatus: {
        province: false,
        city: false,
        area: false
      },
      province: [],
      city: [],
      area: [],
      selectedInfo: {
        province: "",
        city: "",
        area: ""
      },
      isFocus: false,
      cityFlag: false,
      areaFlag: false,
      hasArea: true,
      optionPanelPosition: {
        province: {},
        city: {},
        area: {},
      }
    };
  },
  methods: {
    toggleOptionPanel(val) {
      for (let i in this.optionPanelStatus) {
        if (i === val) {
          this.optionPanelStatus[i] = !this.optionPanelStatus[i];
        } else {
          this.optionPanelStatus[i] = false;
        }
      }
      // this.isFocus = !this.isFocus;
      if (this.optionPanelStatus[val]) {
        if (val === "province") {
          document.body.appendChild(this.$refs['apOptionPanel-province']);
          this.setOptionPanelPosition(val);
          this.showProvince(val);
        }else if (val === "city") {
          document.body.appendChild(this.$refs['apOptionPanel-city']);
          this.setOptionPanelPosition(val);
        } else if (val === "area") {
          document.body.appendChild(this.$refs['apOptionPanel-area']);
          this.setOptionPanelPosition(val);
        }
      } else {
        this.closeOptionPanel(val);
      }
    },
    setOptionPanelPosition(val) {
      let position = {
        top: '',
        left: '',
      };
      if (this.$refs[`apSelectResult-${val}`]) {
        let apSelectResultPosition = this.$refs[`apSelectResult-${val}`].getBoundingClientRect();
        position.top = apSelectResultPosition.top + apSelectResultPosition.height;
        position.left = apSelectResultPosition.left;
      }
      this.optionPanelPosition[`${val}`].top = `${position.top}px`;
      this.optionPanelPosition[`${val}`].left = `${position.left}px`;
    },
    closeOptionPanel(val) {
      this.optionPanelStatus[val] = false;
      // console.log(this.selectedInfo);
    },
    showProvince(val) {
      if (this.optionPanelStatus[val] && this.province.length === 0) {
        for (let i = 0, len = AREA_INFO.length; i < len; i++) {
          this.province.push({
            id: AREA_INFO[i].id,
            name: AREA_INFO[i].name
          });
        }
        // console.log(this.province);
      }
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
      this.cityFlag = true;
      this.closeOptionPanel("province");
    },
    showCity(target) {
      for (let i = 0, len = AREA_INFO.length; i < len; i++) {
        if (AREA_INFO[i].name === target.innerText) {
          this.city = AREA_INFO[i].children;
          break;
        }
      }
      if (this.city[0].children) {
        this.hasArea = true;
      } else {
        this.hasArea = false;
      }
      // console.log(this.city);
    },
    selectCity(e) {
      let target = e.target;
      if (target.nodeName.toLowerCase() === "span") {
        this.selectedInfo.city = target.innerText;
      }
      this.showArea(target);
      this.areaFlag = true;
      this.closeOptionPanel("city");
    },
    showArea(target) {
      for (let i = 0, len = this.city.length; i < len; i++) {
        if (this.city[i].name === target.innerText) {
          this.area = this.city[i].children;
          break;
        }
      }
    },
    selectArea(e) {
      let target = e.target;
      if (target.nodeName.toLowerCase() === "span") {
        this.selectedInfo.area = target.innerText;
        this.closeOptionPanel("area");
      }
    }
  }
};
</script>

<style scoped>
.triple-area-picker {
  font-size: 12px;

  & .province,
  & .city,
  & .area {
    display: inline-block;
    width: 150px;
  }

  & .ap-select-result {
    position: relative;

    & + .ap-select-result {
      margin-left: 10px;
    }

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

    &.disabled{
      cursor: not-allowed;
    }
  }

  & .ap-input-icon {
    position: absolute;
    top: 50%;
    right: 8px;
    transform: translateY(-50%);
    color: #9e9e9e;
    cursor: pointer;

     &.disabled{
      cursor: not-allowed;
    }
  }

}

.ap-option-panel {
  position: absolute;
  top: 0;
  box-sizing: border-box;
  width: 150px;
  padding: 10px 20px;
  border: 1px solid #9e9e9e7a;
  border-radius: 4px;
  margin-top: 8px;
  box-shadow: 0 2px 4px #9e9e9e7a;
  font-size: 12px;
  background: #fff;

  & .option-panel-body {
    height: 240px;
    overflow: scroll;
  }

  & .ap-table-cell {
    display: inline-block;
    width: 100%;
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