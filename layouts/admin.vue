<template>
  <div class="container">
    <div class="main">
      <div class="tab-menu">
        <div class="icon">
          <a>
            <img width="65" height="45" src="../assets/images/logo_1.png" />
          </a>
        </div>
        <ul>
          <li
            v-for="(el, key) in dataMenu"
            :key="key"
            :class="{ focus: key === isFocus }"
            @click="handleFocusMenu(el.value, el.router)"
          >
            <span :style="'padding-left:24px'"
              ><svg
                viewBox="64 64 896 896"
                focusable="false"
                :data-icon="el.dataIcon"
                width="1em"
                height="1em"
                fill="currentColor"
                aria-hidden="true"
              >
                <path :d="el.path"></path></svg></span
            ><span :style="'padding-left: 14px'"
              ><a>{{ el.name }}</a></span
            >
          </li>
        </ul>
      </div>
    </div>
    <div class="header">
      <div class="header-menu">
        <div class="header-item">
          <span
            ><span class="notify"
              ><svg
                viewBox="64 64 896 896"
                focusable="false"
                data-icon="bell"
                width="1em"
                height="1em"
                fill="currentColor"
                aria-hidden="true"
                :style="'font-size: 1.6rem;font-weight: 600;color: #fff;cursor: pointer;'"
              >
                <path
                  d="M816 768h-24V428c0-141.1-104.3-257.7-240-277.1V112c0-22.1-17.9-40-40-40s-40 17.9-40 40v38.9c-135.7 19.4-240 136-240 277.1v340h-24c-17.7 0-32 14.3-32 32v32c0 4.4 3.6 8 8 8h216c0 61.8 50.2 112 112 112s112-50.2 112-112h216c4.4 0 8-3.6 8-8v-32c0-17.7-14.3-32-32-32zM512 888c-26.5 0-48-21.5-48-48h96c0 26.5-21.5 48-48 48zM304 768V428c0-55.6 21.6-107.8 60.9-147.1S456.4 220 512 220c55.6 0 107.8 21.6 147.1 60.9S720 372.4 720 428v340H304z"
                ></path></svg></span></span
          ><span :style="'margin-left: 20px;cursor:pointer'" @click="showMenu"
            ><span class="avatar"
              ><svg
                viewBox="64 64 896 896"
                focusable="false"
                data-icon="user"
                width="1em"
                height="1em"
                fill="currentColor"
                aria-hidden="true"
              >
                <path
                  d="M858.5 763.6a374 374 0 00-80.6-119.5 375.63 375.63 0 00-119.5-80.6c-.4-.2-.8-.3-1.2-.5C719.5 518 760 444.7 760 362c0-137-111-248-248-248S264 225 264 362c0 82.7 40.5 156 102.8 201.1-.4.2-.8.3-1.2.5-44.8 18.9-85 46-119.5 80.6a375.63 375.63 0 00-80.6 119.5A371.7 371.7 0 00136 901.8a8 8 0 008 8.2h60c4.4 0 7.9-3.5 8-7.8 2-77.2 33-149.5 87.8-204.3 56.7-56.7 132-87.9 212.2-87.9s155.5 31.2 212.2 87.9C779 752.7 810 825 812 902.2c.1 4.4 3.6 7.8 8 7.8h60a8 8 0 008-8.2c-1-47.8-10.9-94.3-29.5-138.2zM512 534c-45.9 0-89.1-17.9-121.6-50.4S340 407.9 340 362c0-45.9 17.9-89.1 50.4-121.6S466.1 190 512 190s89.1 17.9 121.6 50.4S684 316.1 684 362c0 45.9-17.9 89.1-50.4 121.6S557.9 534 512 534z"
                ></path>
              </svg>
            </span>
          </span>
          <div class="menu-dropdown" :class="{ displayMenu: !isShowMenu }">
            <ul>
              <li>
                <a @click="handleRedirect('/admin/profile')"
                  >Thông tin cá nhân</a
                >
              </li>
              <li><a @click="handleRedirect('/admin/profile')">Cài đặt</a></li>

              <li><a @click="handleLogout">Đăng xuất</a></li>
            </ul>
          </div>
        </div>
      </div>
      <div v-if="loading" class="loading">
        <a-spin class="outer">
          <a-icon
            slot="indicator"
            type="loading"
            style="font-size: 48px"
            spin
          />
        </a-spin>
      </div>
      <div class="child">
        <Nuxt />
      </div>
    </div>
  </div>
</template>

<script>
import { mapFields } from "vuex-map-fields";
export default {
  data() {
    return {
      isFocus: 0,
      isShowMenu: false,
      dataMenu: [
        {
          dataIcon: "dashboard",
          path: "M924.8 385.6a446.7 446.7 0 00-96-142.4 446.7 446.7 0 00-142.4-96C631.1 123.8 572.5 112 512 112s-119.1 11.8-174.4 35.2a446.7 446.7 0 00-142.4 96 446.7 446.7 0 00-96 142.4C75.8 440.9 64 499.5 64 560c0 132.7 58.3 257.7 159.9 343.1l1.7 1.4c5.8 4.8 13.1 7.5 20.6 7.5h531.7c7.5 0 14.8-2.7 20.6-7.5l1.7-1.4C901.7 817.7 960 692.7 960 560c0-60.5-11.9-119.1-35.2-174.4zM761.4 836H262.6A371.12 371.12 0 01140 560c0-99.4 38.7-192.8 109-263 70.3-70.3 163.7-109 263-109 99.4 0 192.8 38.7 263 109 70.3 70.3 109 163.7 109 263 0 105.6-44.5 205.5-122.6 276zM623.5 421.5a8.03 8.03 0 00-11.3 0L527.7 506c-18.7-5-39.4-.2-54.1 14.5a55.95 55.95 0 000 79.2 55.95 55.95 0 0079.2 0 55.87 55.87 0 0014.5-54.1l84.5-84.5c3.1-3.1 3.1-8.2 0-11.3l-28.3-28.3zM490 320h44c4.4 0 8-3.6 8-8v-80c0-4.4-3.6-8-8-8h-44c-4.4 0-8 3.6-8 8v80c0 4.4 3.6 8 8 8zm260 218v44c0 4.4 3.6 8 8 8h80c4.4 0 8-3.6 8-8v-44c0-4.4-3.6-8-8-8h-80c-4.4 0-8 3.6-8 8zm12.7-197.2l-31.1-31.1a8.03 8.03 0 00-11.3 0l-56.6 56.6a8.03 8.03 0 000 11.3l31.1 31.1c3.1 3.1 8.2 3.1 11.3 0l56.6-56.6c3.1-3.1 3.1-8.2 0-11.3zm-458.6-31.1a8.03 8.03 0 00-11.3 0l-31.1 31.1a8.03 8.03 0 000 11.3l56.6 56.6c3.1 3.1 8.2 3.1 11.3 0l31.1-31.1c3.1-3.1 3.1-8.2 0-11.3l-56.6-56.6zM262 530h-80c-4.4 0-8 3.6-8 8v44c0 4.4 3.6 8 8 8h80c4.4 0 8-3.6 8-8v-44c0-4.4-3.6-8-8-8z",
          name: "Bảng điều khiển",
          value: 0,
          router: "admin/dashboard",
        },

        {
          dataIcon: "funds",
          path: "M928 140H96c-17.7 0-32 14.3-32 32v496c0 17.7 14.3 32 32 32h380v112H304c-8.8 0-16 7.2-16 16v48c0 4.4 3.6 8 8 8h432c4.4 0 8-3.6 8-8v-48c0-8.8-7.2-16-16-16H548V700h380c17.7 0 32-14.3 32-32V172c0-17.7-14.3-32-32-32zm-40 488H136V212h752v416z",
          name: "Quản lý quỹ",
          value: 1,
          router: "admin/funds",
        },
        {
          dataIcon: "calculator",
          path: "M251.2 387H320v68.8c0 1.8 1.8 3.2 4 3.2h48c2.2 0 4-1.4 4-3.3V387h68.8c1.8 0 3.2-1.8 3.2-4v-48c0-2.2-1.4-4-3.3-4H376v-68.8c0-1.8-1.8-3.2-4-3.2h-48c-2.2 0-4 1.4-4 3.2V331h-68.8c-1.8 0-3.2 1.8-3.2 4v48c0 2.2 1.4 4 3.2 4zm328 0h193.6c1.8 0 3.2-1.8 3.2-4v-48c0-2.2-1.4-4-3.3-4H579.2c-1.8 0-3.2 1.8-3.2 4v48c0 2.2 1.4 4 3.2 4zm0 265h193.6c1.8 0 3.2-1.8 3.2-4v-48c0-2.2-1.4-4-3.3-4H579.2c-1.8 0-3.2 1.8-3.2 4v48c0 2.2 1.4 4 3.2 4zm0 104h193.6c1.8 0 3.2-1.8 3.2-4v-48c0-2.2-1.4-4-3.3-4H579.2c-1.8 0-3.2 1.8-3.2 4v48c0 2.2 1.4 4 3.2 4zm-195.7-81l61.2-74.9c4.3-5.2.7-13.1-5.9-13.1H388c-2.3 0-4.5 1-5.9 2.9l-34 41.6-34-41.6a7.85 7.85 0 00-5.9-2.9h-50.9c-6.6 0-10.2 7.9-5.9 13.1l61.2 74.9-62.7 76.8c-4.4 5.2-.8 13.1 5.8 13.1h50.8c2.3 0 4.5-1 5.9-2.9l35.5-43.5 35.5 43.5c1.5 1.8 3.7 2.9 5.9 2.9h50.8c6.6 0 10.2-7.9 5.9-13.1L383.5 675zM880 112H144c-17.7 0-32 14.3-32 32v736c0 17.7 14.3 32 32 32h736c17.7 0 32-14.3 32-32V144c0-17.7-14.3-32-32-32zm-36 732H180V180h664v664z",
          name: "Quản lý người dùng",
          value: 2,
          router: "admin/manager-users",
        },
        {
          dataIcon: "history",
          path: "M536.1 273H488c-4.4 0-8 3.6-8 8v275.3c0 2.6 1.2 5 3.3 6.5l165.3 120.7c3.6 2.6 8.6 1.9 11.2-1.7l28.6-39c2.7-3.7 1.9-8.7-1.7-11.2L544.1 528.5V281c0-4.4-3.6-8-8-8zm219.8 75.2l156.8 38.3c5 1.2 9.9-2.6 9.9-7.7l.8-161.5c0-6.7-7.7-10.5-12.9-6.3L752.9 334.1a8 8 0 003 14.1zm167.7 301.1l-56.7-19.5a8 8 0 00-10.1 4.8c-1.9 5.1-3.9 10.1-6 15.1-17.8 42.1-43.3 80-75.9 112.5a353 353 0 01-112.5 75.9 352.18 352.18 0 01-137.7 27.8c-47.8 0-94.1-9.3-137.7-27.8a353 353 0 01-112.5-75.9c-32.5-32.5-58-70.4-75.9-112.5A353.44 353.44 0 01171 512c0-47.8 9.3-94.2 27.8-137.8 17.8-42.1 43.3-80 75.9-112.5a353 353 0 01112.5-75.9C430.6 167.3 477 158 524.8 158s94.1 9.3 137.7 27.8A353 353 0 01775 261.7c10.2 10.3 19.8 21 28.6 32.3l59.8-46.8C784.7 146.6 662.2 81.9 524.6 82 285 82.1 92.6 276.7 95 516.4 97.4 751.9 288.9 942 524.8 942c185.5 0 343.5-117.6 403.7-282.3 1.5-4.2-.7-8.9-4.9-10.4z",
          name: "Lịch sử giao dịch",
          value: 3,
          router: "admin/history",
        },
        {
          dataIcon: "profile",
          path: "M880 112H144c-17.7 0-32 14.3-32 32v736c0 17.7 14.3 32 32 32h736c17.7 0 32-14.3 32-32V144c0-17.7-14.3-32-32-32zm-40 728H184V184h656v656zM492 400h184c4.4 0 8-3.6 8-8v-48c0-4.4-3.6-8-8-8H492c-4.4 0-8 3.6-8 8v48c0 4.4 3.6 8 8 8zm0 144h184c4.4 0 8-3.6 8-8v-48c0-4.4-3.6-8-8-8H492c-4.4 0-8 3.6-8 8v48c0 4.4 3.6 8 8 8zm0 144h184c4.4 0 8-3.6 8-8v-48c0-4.4-3.6-8-8-8H492c-4.4 0-8 3.6-8 8v48c0 4.4 3.6 8 8 8zM340 368a40 40 0 1080 0 40 40 0 10-80 0zm0 144a40 40 0 1080 0 40 40 0 10-80 0zm0 144a40 40 0 1080 0 40 40 0 10-80 0z",
          name: "Thông tin cá nhân",
          value: 4,
          router: "admin/profile",
        },
      ],
    };
  },
  computed: {
    ...mapFields({
      loading: "loading",
    }),
  },
  watch: {
    $route: {
      handler() {
        this.setFocusMenu();
      },
      deep: true,
    },
  },
  mounted() {
    this.setFocusMenu();
  },
  methods: {
    handleRedirect(route) {
      this.$router.push(route);
      this.isShowMenu = !this.isShowMenu;
    },
    handleFocusMenu(value, router) {
      this.isFocus = value;
      this.$router.push(`/${router}`);
    },
    showMenu() {
      this.isShowMenu = !this.isShowMenu;
    },
    setFocusMenu() {
      this.isFocus = this.dataMenu.find((item) =>
        this.$route.fullPath.includes(item.router),
      )?.value;
    },
    async handleLogout() {
      this.$store.commit("SET_LOADING", true);
      await this.$auth.logout();
      this.$router.push("/login");
      this.$store.commit("SET_LOADING", false);
    },
  },
};
</script>

<style lang="scss" scoped>
body {
  padding: 0 !important;
  margin: 0 !important;
}
.container {
  display: flex;
  flex-direction: row;
}
ul {
  padding: 0;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0;
}
li {
  list-style: none;
  color: #000000;
  margin-bottom: 16px;
  padding: 10px 0 6px 0;
  width: 100%;
  cursor: pointer;
}
a {
  text-decoration: none;
  max-height: 3.6rem;
}
.main {
  height: 100vh;
  display: flex;
  flex-direction: row;
  .tab-menu {
    width: 200px;
    min-width: 200px;
    display: flex;
    align-items: center;
    flex-direction: column;
    font-size: 12px;
    .focus {
      color: green;
      background: #bacfc0;
      border-right-width: 6px;
      border-right-style: solid;
    }
    .icon {
      width: 100%;
      display: flex;
      justify-content: center;
    }
  }
}
.header {
  display: flex;
  flex-direction: column;
  width: 100%;
  .header-menu {
    max-height: 49px;
    height: 49px;
    background: green;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    .header-item {
      display: flex;
      flex-direction: row;
      padding-right: 25px;
      position: relative;
      .menu-dropdown {
        position: absolute;
        top: 42px;
        right: 25px;
        z-index: 111;
        max-width: 160px;
        width: 160px;
        background: #fff;
        border-radius: 3px;
        transition: all 0.5s ease;
        a {
          padding: 10px 10px 0 10px;
        }
      }
    }
  }
  .child {
    background: #d8d8d8;
    width: 100%;
    height: calc(100vh - 49px);
    max-height: calc(100vh - 49px);
    padding: 20px;
    overflow-y: auto;
  }
}

.avatar {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: beige;
  border-radius: 50%;
}
.notify {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.displayMenu {
  display: none;
}
.loading {
  position: fixed;
  z-index: 99999;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  .outer {
    display: flex;
    min-height: 100%;
    width: 100%;
    justify-content: center;
    align-items: center;
    background-color: rgba(255, 255, 255, 0.7);
  }
}
</style>
