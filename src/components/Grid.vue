<template>
  <div class="overflow-x-hidden">
    <div class="container">
      <div class="grid">
        <div
          class="grid-item"
          v-for="item in gridItems"
          :key="item.id"
          :class="{'flashing': item.flash}"
        >
          <div class="ball-container">
            <div class="corner-ball" v-if="item.hasBall"></div>
          </div>
        </div>
      </div>
    </div>
    <div class="menu-container" ref="sidebar" v-click-outside="closeSidebar">
      <button class="toggle-button" @click="toggleSidebar">{{ sidebarExpanded ? '收合' : '展開' }}</button>
      <transition name="slide">
        <div class="sidebar" v-show="sidebarExpanded" @click.stop>
          <ul>
            <li v-for="category in categories" :key="category.key">
              <div class="category" @click="toggleCategory(category)" :class="{ 'active': category.isExpanded }">
                {{ category.text }}
                <span class="arrow">{{ category.isExpanded ? ' ▼' : ' ▶' }}</span>
              </div>
              <ul v-show="category.isExpanded" :class="{ 'active': category.isExpanded }">
                <li v-for="item in category.children" :key="item.key">
                  <div class="item" @click="toggleItem(item)" :class="{ 'active': item.isExpanded }">
                    {{ item.text }}
                    <span class="arrow" v-if="item.children && item.children.length > 0">{{ item.isExpanded ? ' ▼' : ' ▶' }}</span>
                  </div>
                  <ul v-show="item.isExpanded" >
                    <li v-for="subItem in item.children" :key="subItem.key" class="sub-item">
                      <div class="detail-item" @click="toggleDetail(subItem)" :class="{ 'active': subItem.isActive }">
                        {{ subItem.text }}
                      </div>
                    </li>
                  </ul>
                </li>
              </ul>
            </li>
          </ul>
        </div>
      </transition>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      categories: [
        {
          key: "64f",
          text: "好喝黑糖",
          children: [
            {
              key: "445",
              text: "黑糖鮮奶",
              children: [
                {
                  key: "37a",
                  text: "黑糖珍珠鮮奶"
                },
                {
                  key: "feb",
                  text: "黑糖芋圓鮮奶"
                },
                {
                  key: "59c",
                  text: "黑糖蒟蒻鮮奶"
                }
              ]
            },
            {
              key: "29e",
              text: "黑糖冬瓜",
              children: [
                {
                  key: "ac3",
                  text: "黑糖冬瓜牛奶"
                },
                {
                  key: "ca0",
                  text: "黑糖冬瓜珍珠"
                }
              ]
            }
          ]
        },
        {
          key: "6c3",
          text: "茶",
          children: [
            {
              key: "5dc",
              text: "烏龍綠"
            },
            {
              key: "b5f",
              text: "綠茶"
            },
            {
              key: "b09",
              text: "紅茶"
            },
            {
              key: "887",
              text: "青茶"
            }
          ]
        },
        {
          key: "c81",
          text: "咖啡",
          children: [
            {
              key: "e02",
              text: "黑咖啡",
              children: [
                {
                  key: "d20",
                  text: "濃縮咖啡"
                },
                {
                  key: "1f8",
                  text: "美式咖啡"
                }
              ]
            },
            {
              key: "d7a",
              text: "牛奶咖啡",
              children: [
                {
                  key: "c09",
                  text: "拿鐵",
                  children: [
                    {
                      key: "db2",
                      text: "黑糖拿鐵"
                    },
                    {
                      key: "9f6",
                      text: "榛果拿鐵"
                    },
                    {
                      key: "b61",
                      text: "香草拿鐵"
                    }
                  ]
                },
                {
                  key: "9ac",
                  text: "卡布奇諾"
                },
                {
                  key: "ce8",
                  text: "摩卡"
                }
              ]
            }
          ]
        }
      ],
      sidebarExpanded: false,
      isShow: false,
      gridItems: [
        { id: 1, flash: false, hasBall: true },
        { id: 2, flash: false, hasBall: false },
        { id: 3, flash: false, hasBall: true },
        { id: 4, flash: false, hasBall: false },
        { id: 5, flash: false, hasBall: false },
        { id: 6, flash: false, hasBall: false },
        { id: 7, flash: false, hasBall: true },
        { id: 8, flash: false, hasBall: false },
        { id: 9, flash: false, hasBall: true },
      ],
      ballPositions: ["0px", "0px", "0px", "0px"], // 初始位置

    };

  },
  mounted() {
    // 判断点击的是否選單
    document.addEventListener("click", this.closeSidebar);
    // 閃爍
    this.startFlashing();
  },
  destroyed() {
    document.removeEventListener("click", this.closeSidebar);
  },
  methods: {
    //页面其他区域点击关闭分类
    closeSidebar(e) {
      let self = this;
      if (self.sidebarExpanded) {
        //排除本身
        let sidebar = self.$refs.sidebar
        if(sidebar){
          if (!sidebar.contains(e.target)) {
            self.sidebarExpanded = false;
          }
        }
      }
    },
    toggleSidebar() {
      if (this.sidebarExpanded && event.target.closest(".sidebar")) {
        return;
      }
      this.sidebarExpanded = !this.sidebarExpanded;
    },
    toggleCategory(selectedCategory) {
      this.removeActive()
      this.categories.forEach(category => {
        if (category !== selectedCategory) {
          category.isExpanded = false; // 关闭其他类别
        }
      });
      selectedCategory.isExpanded = !selectedCategory.isExpanded;
    },
    toggleItem(selectedItem) {
      this.removeActive()
      this.categories.forEach(category => {
        category.children.forEach(item => {
          if (item !== selectedItem) {
            item.isExpanded = false; // 关闭其他项目
          }
        });
      });
      selectedItem.isExpanded = !selectedItem.isExpanded;
    },
    toggleDetail(curItem) {
      this.removeActive()
      curItem.isActive = !curItem.isActive
    },
    removeActive() {
      this.categories.forEach(category => {
        category.children.forEach(item => {
          if (item.children !== undefined) {
            item.children.forEach(item => {
              item.isActive = false
            });
          }
        });
      });
    },
    startFlashing() {
      setInterval(() => {
        this.gridItems.forEach((item, index) => {
          item.flash = index === 2 || index === 4 || index === 8 ? !item.flash : item.flash;
        });
      }, 500);
    },
    
  }
};
</script>
<style scoped>
.sidebar {
  position: absolute;
  display: block;
  top: 0;
  right: 0px;
  width: 200px;
  background-color: rgba(23, 23, 23, 0.9);;
  color: #fff;
  border: 1px solid #fff;
  height: 100%;
  overflow-y: auto;
}

/* 定义从右侧滑入的过渡动画 */
.slide-enter-active, .slide-leave-active {
  transition: all 0.5s linear;
}
.slide-enter-from {
  transform: translateX(200px);
}
/* .slide-enter-to {
  transform: translateX(100px);
}
.slide-leave-from {
  transform: translateX(100px);
} */
.slide-leave-to {
  transform: translateX(200px);
}
.toggle-button {
  margin-bottom: 8px;
  position: absolute;
  background-color: #d0cece;
  right: 0;
  top: 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

.category, .item {
  display: flex;
  align-items: center;
  cursor: pointer;
  padding: 5px;
}
.category.active, .item.active, .detail-item.active {
  color: rgb(255, 204, 37);
}
ul.active, .category.active {
  background: #7f7c7c;
}
.arrow {
  padding-left: auto;
}

ul ul {
  padding-left: 16px;
}

.sub-item {
  padding-left: 16px;
}
/* 九空格 */
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  width:100%;
  height: 100vh;
  position: relative;
}

.grid {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  grid-gap: 10px;
}

.grid-item {
  width: 100px;
  height: 100px;
  border: black solid 2px;
  background: radial-gradient(circle, rgba(113, 81, 95, 1) 81%, rgba(0, 0, 0, 1) 100%);
  transition: opacity 0.5s ease-in-out;
}

.flashing {
  /* opacity: 0.6; */
  background: radial-gradient(circle, rgba(113, 81, 95, 0.6) 81%, rgba(0, 0, 0, 1) 100%);
}

.ball-container {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.corner-ball {
  width: 30px;
  height: 30px;
  background-color: #A5F12B;
  border-radius: 50%;
  position: relative;
  top: 0;
  left: 0;
  
  animation: moveRight 1.5s ease infinite;
}

@keyframes moveRight {
  0% {
    left: 0;
  }
  100% {
    left: 250px;
  }
}
</style>