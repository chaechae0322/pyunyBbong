<template>
  <div class="body-container">
    <div class="section over-hide z-bigger">
      <div class="section over-hide z-bigger">
        <div class="checkbox-container">
          <div class="row justify-content-center">
            <div class="conv-tab" style="margin-top: 15px;">
              <input
                class="checkbox-convenience"
                type="checkbox"
                name="convenience"
                id="convenience-0"
                value="all"
                v-model="convenience_all"
              />
              <label class="for-checkbox-convenience" for="convenience-0">
                <img class="conv_icon" src="@/assets/icons/all.png" alt />
              </label>
              <!--
              -->
              <input
                class="checkbox-convenience"
                type="checkbox"
                name="convenience"
                id="convenience-1"
                value="646"
                v-model="conveniences"
              />
              <label class="for-checkbox-convenience" for="convenience-1">
                <img class="conv_icon" src="@/assets/icons/gs25.png" alt />
              </label>
              <!--
              -->
              <input
                class="checkbox-convenience"
                type="checkbox"
                name="convenience"
                id="convenience-2"
                value="682"
                v-model="conveniences"
              />
              <label class="for-checkbox-convenience" for="convenience-2">
                <img class="conv_icon" src="@/assets/icons/cu.png" alt />
              </label>
              <!--
              -->
              <input
                class="checkbox-convenience"
                type="checkbox"
                name="convenience"
                id="convenience-3"
                value="936"
                v-model="conveniences"
              />
              <label class="for-checkbox-convenience" for="convenience-3">
                <img class="conv_icon" src="@/assets/icons/emart.jpg" alt />
              </label>
              <!--
              -->
              <input
                class="checkbox-convenience"
                type="checkbox"
                name="convenience"
                id="convenience-4"
                value="970"
                v-model="conveniences"
              />
              <label class="for-checkbox-convenience" for="convenience-4">
                <img class="conv_icon" src="@/assets/icons/seven.png" alt />
              </label>
              <!--
              -->
              <input
                class="checkbox-convenience"
                type="checkbox"
                name="convenience"
                id="convenience-5"
                value="756"
                v-model="conveniences"
              />
              <label class="for-checkbox-convenience" for="convenience-5">
                <img class="conv_icon" src="@/assets/icons/ministop.png" alt />
              </label>
              <!--
              -->
            </div>
            <!-- 슬라이드 들어가는 부분 -->
            <!-- v-model -->
            <div class="main-slide">
              <b-tabs
                active-nav-item-class="font-weight-bold text-uppercase text-danger"
                content-class="mt-3"
                justified
              >
                <!-- 할인 정보 -->
                <b-tab title="할인 정보" @click="tabClick('sale')" active>
                  <item-card :items="saleShowItems"></item-card>
                  <img
                    v-if="saleShowItems.length === 0"
                    src="@/assets/images/none_main.png"
                    alt="준비 중입니다."
                    width="100%"
                  />
                </b-tab>
                <infinite-loading
                  v-show="selectedTab == 'sale'"
                  @infinite="infiniteHandler"
                  force-use-infinite-wrapper="#__BVID__12__BV_tab_container_"
                ></infinite-loading>
                <!-- 상품 정보 -->
                <b-tab title="상품 정보" @click="tabClick('all')">
                  <item-card :items="allShowItems"></item-card>
                  <img
                    v-if="allShowItems.length === 0"
                    src="@/assets/images/none_main.png"
                    alt="준비 중입니다."
                    width="100%"
                  />
                </b-tab>
                <infinite-loading
                  v-show="selectedTab == 'all'"
                  @infinite="infiniteHandler"
                  force-use-infinite-wrapper="#__BVID__19__BV_tab_container_"
                ></infinite-loading>
              </b-tabs>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import productAxios from "../api/Productaxios";
import InfiniteLoading from "vue-infinite-loading";
import ItemCard from "@/components/ItemCard.vue";

export default {
  name: "MainInfo",
  components: {
    InfiniteLoading,
    ItemCard
  },
  data() {
    return {
      selectedTab: "sale",
      convenience_all: ["all"],
      conveniences: [],

      saleItems: [],
      saleFilteredItems: [],
      saleShowItems: [],
      saleItemIdx: 0,

      allItems: [],
      allFilteredItems: [],
      allShowItems: [],
      allItemIdx: 0
    };
  },
  methods: {
    infiniteHandler($state) {
      if (this.selectedTab == "sale") {
        setTimeout(() => {
          this.saleShowItems = this.saleShowItems.concat(
            this.saleFilteredItems.splice(0, 12)
          );
          this.saleItemIdx += 6;
          $state.loaded();
        }, 500);
      } else if (this.selectedTab == "all") {
        setTimeout(() => {
          this.allShowItems = this.allShowItems.concat(
            this.allFilteredItems.splice(0, 12)
          );
          this.allItemIdx += 6;
          $state.loaded();
        }, 500);
      }
    },
    tabClick(tab) {
      if (tab == "sale") {
        this.selectedTab = "sale";
      } else if (tab == "all") {
        this.selectedTab = "all";
      }
    }
  },
  mounted() {
    productAxios.getSale(
      res => {
        this.saleItems = Object.assign([], res.data);
        this.saleFilteredItems = Object.assign([], res.data);
        this.saleShowItems = this.saleFilteredItems.splice(0, 12);
      },
      err => {
        console.log(err);
      }
    );
    productAxios.getProduct(
      res => {
        res.data.forEach(item => {
          item.product = {
            id: item.id,
            name: item.name,
            image: item.image,
            price: item.price
          };
        });
        this.allItems = res.data;
        this.allFilteredItems = res.data;
        this.allShowItems = this.allFilteredItems.splice(0, 12);
      },
      err => {
        console.log(err);
      }
    );
  },
  watch: {
    convenience_all: function() {
      if (this.conveniences.length && this.convenience_all.length) {
        this.conveniences = [];
        if (this.selectedTab == "sale") {
          this.saleFilteredItems = Object.assign([], this.saleItems);
          this.saleShowItems = this.saleFilteredItems.splice(0, 12);
        } else if (this.selectedTab == "all") {
          this.allFilteredItems = Object.assign([], this.allItems);
          this.allShowItems = this.allFilteredItems.splice(0, 12);
        }
      } else if (!this.conveniences.length && !this.convenience_all.length) {
        this.convenience_all = ["all"];
        document.getElementById("convenience-0").checked = true;
      }
    },
    conveniences: function() {
      if (this.conveniences.length && this.convenience_all.length) {
        this.convenience_all = [];
        if (this.selectedTab == "sale") {
          this.saleFilteredItems = Object.assign([], this.saleItems);
          this.saleShowItems = this.saleFilteredItems.splice(0, 12);
        } else if (this.selectedTab == "all") {
          this.allFilteredItems = Object.assign([], this.allItems);
          this.allShowItems = this.allFilteredItems.splice(0, 12);
        }
      } else if (!this.conveniences.length && !this.convenience_all.length) {
        this.convenience_all = ["all"];
      }
      if (this.conveniences.length) {
        if (this.selectedTab == "sale") {
          this.saleFilteredItems = this.saleItems.filter(item => {
            return this.conveniences.includes(item.franchiseId.toString());
          });
          this.saleShowItems = this.saleFilteredItems.splice(0, 12);
        } else if (this.selectedTab == "all") {
          this.allFilteredItems = this.allItems.filter(item => {
            return this.conveniences.includes(item.franchiseId.toString());
          });
          this.allShowItems = this.allFilteredItems.splice(0, 12);
        }
      }
    }
  }
};
</script>

<style scoped>
@font-face {
  font-family: "Kyobo";
  src: url("https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_20-04@1.0/KyoboHand.woff")
    format("woff");
  font-weight: normal;
  font-style: normal;
}

:root {
  --white: #ffffff;
  --light: #f0eff3;
  --black: #000000;
  --dark-blue: #1f2029;
  --dark-light: #353746;
  --red: #da2c4d;
  --yellow: #f8ab37;
  --grey: #ecedf3;
}

/* #Primary
================================================== */

.body-container {
  width: 100%;
  height: auto;
  background-color: var(--white);
  overflow-x: hidden;
  font-size: 17px;
  line-height: 30px;
  -webkit-transition: all 300ms linear;
  transition: all 300ms linear;
  font-family: "Kyobo";
}
p {
  font-size: 17px;
  line-height: 30px;
  color: var(--black);
  letter-spacing: 1px;
  font-weight: 500;
  -webkit-transition: all 300ms linear;
  transition: all 300ms linear;
}
::selection {
  color: var(--black);
  background-color: var(--white);
}
::-moz-selection {
  color: var(--black);
  background-color: var(--white);
}
mark {
  color: var(--black);
  background-color: var(--white);
}
.section {
  position: relative;
  width: 100%;
  display: block;
  text-align: center;
  margin: 0 auto;
}
.over-hide {
  overflow: hidden;
}
.z-bigger {
  z-index: 100 !important;
}

.background-color {
  top: 0;
  left: 0;
  width: 100%;
  height: auto;
  background-color: var(--white);
  z-index: 1;
  -webkit-transition: all 300ms linear;
  transition: all 300ms linear;
}
.checkbox:checked ~ .background-color {
  background-color: var(--dark-blue);
}

[type="checkbox"]:checked,
[type="checkbox"]:not(:checked) {
  position: absolute;
  left: -9999px;
  width: 0;
  height: 0;
  visibility: hidden;
}

.conv_icon {
  width: 8vw;
  max-width: 65px;
  min-width: 32px;
}

.checkbox:checked + label,
.checkbox:not(:checked) + label {
  position: relative;
  width: 70px;
  display: inline-block;
  padding: 0;
  margin: 0 auto;
  text-align: center;
  margin: 100px 0 30px 55%;
  height: 6px;
  border-radius: 4px;
  background-image: linear-gradient(298deg, var(--red), var(--yellow));
  z-index: 1 !important;
}
.checkbox:checked + label:before,
.checkbox:not(:checked) + label:before {
  position: absolute;
  font-family: "unicons";
  cursor: pointer;
  top: -17px;
  z-index: 2;
  font-size: 20px;
  line-height: 40px;
  text-align: center;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  -webkit-transition: all 300ms linear;
  transition: all 300ms linear;
}
.checkbox:not(:checked) + label:before {
  content: "\eac1";
  left: 0;
  color: var(--yellow);
  background-color: var(--dark-blue);
  box-shadow: 0 4px 4px rgba(0, 0, 0, 0.15), 0 0 0 1px rgba(26, 53, 71, 0.07);
}
.checkbox:checked + label:before {
  content: "\eb8f";
  left: 30px;
  color: var(--grey);
  background-color: var(--white);
  box-shadow: 0 4px 4px rgba(26, 53, 71, 0.25), 0 0 0 1px rgba(26, 53, 71, 0.07);
}

.checkbox:checked ~ .section .checkbox-container .row .conv-tab p {
  color: var(--white);
}

.checkbox-convenience:checked + label,
.checkbox-convenience:not(:checked) + label {
  position: relative;
  display: -webkit-inline-flex;
  display: -ms-inline-flexbox;
  display: inline-flex;
  -webkit-align-items: center;
  -moz-align-items: center;
  -ms-align-items: center;
  align-items: center;
  -webkit-justify-content: center;
  -moz-justify-content: center;
  -ms-justify-content: center;
  justify-content: center;
  -ms-flex-pack: center;
  text-align: center;
  padding: 0;
  padding: 3px;
  font-size: 14px;
  line-height: 30px;
  letter-spacing: 1px;
  margin: 0 1.3vw 16px;
  text-align: center;
  border-radius: 4px;
  cursor: pointer;
  color: var(--white);
  text-transform: uppercase;
  background-color: var(--light);
  -webkit-transition: all 300ms linear;
  transition: all 300ms linear;
}
.checkbox-convenience:not(:checked) + label::before {
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.1);
}
.checkbox-convenience:checked + label::before {
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
}
.checkbox-convenience:not(:checked) + label:hover::before {
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
}
.checkbox-convenience:checked + label::before,
.checkbox-convenience:not(:checked) + label::before {
  position: absolute;
  content: "";
  top: -2px;
  left: -2px;
  width: calc(100% + 4px);
  height: calc(100% + 4px);
  border-radius: 4px;
  z-index: -2;
  background-image: linear-gradient(138deg, var(--red), var(--yellow));
  -webkit-transition: all 300ms linear;
  transition: all 300ms linear;
}
.checkbox-convenience:not(:checked) + label::before {
  top: -1px;
  left: -1px;
  width: calc(100% + 2px);
  height: calc(100% + 2px);
}
.checkbox-convenience:checked + label::after,
.checkbox-convenience:not(:checked) + label::after {
  position: absolute;
  content: "";
  top: -2px;
  left: -2px;
  width: calc(100% + 4px);
  height: calc(100% + 4px);
  border-radius: 4px;
  z-index: -2;
  background-color: var(--light);
  -webkit-transition: all 300ms linear;
  transition: all 300ms linear;
}
.checkbox-convenience:checked + label::after {
  opacity: 0;
}
.checkbox-convenience:checked + label .uil,
.checkbox-convenience:not(:checked) + label .uil {
  font-size: 20px;
}
.checkbox-convenience:checked + label .text,
.checkbox-convenience:not(:checked) + label .text {
  position: relative;
  display: inline-block;
  -webkit-transition: opacity 300ms linear;
  transition: opacity 300ms linear;
}
.checkbox-convenience:checked + label .text {
  opacity: 0.6;
}
.checkbox-convenience:checked + label .text::after,
.checkbox-convenience:not(:checked) + label .text::after {
  position: absolute;
  content: "";
  width: 0;
  left: 0;
  top: 50%;
  margin-top: -1px;
  height: 2px;
  background-image: linear-gradient(138deg, var(--red), var(--yellow));
  z-index: 1;
  -webkit-transition: all 300ms linear;
  transition: all 300ms linear;
}
.checkbox-convenience:not(:checked) + label .text::after {
  width: 0;
}
.checkbox-convenience:checked + label .text::after {
  width: 100%;
}

.checkbox:checked
  ~ .section
  .checkbox-container
  .row
  .conv-tab
  .checkbox-convenience:not(:checked)
  + label,
.checkbox:checked
  ~ .section
  .checkbox-container
  .row
  .conv-tab
  .checkbox-convenience:checked
  + label {
  background-color: var(--dark-light);
  color: var(--light);
}
.checkbox:checked
  ~ .section
  .checkbox-container
  .row
  .conv-tab
  .checkbox-convenience:checked
  + label::after,
.checkbox:checked
  ~ .section
  .checkbox-container
  .row
  .conv-tab
  .checkbox-convenience:not(:checked)
  + label::after {
  background-color: var(--dark-light);
}

.link-to-page {
  position: fixed;
  top: 30px;
  right: 30px;
  z-index: 20000;
  cursor: pointer;
  width: 30px;
}
.link-to-page img {
  width: 100%;
  height: auto;
  display: block;
}

.main-slide {
  width: 100%;
}
</style>
