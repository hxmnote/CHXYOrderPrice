<template>
  <div class="page">
    <!-- 顶部状态栏占位 -->
    <div class="status-bar"></div>

    <!-- 顶部栏 -->
    <div class="header">
      <div class="header-brand">晨辉星游</div>
      <div class="header-actions">
        <div class="icon-btn" @click="showQrPopup = true">
          <van-icon name="chat-o" />
          <span>下单找我</span>
        </div>
        <div class="icon-btn" @click="changeRandomIcon">
          <van-icon name="point-gift-o" />
          <span>随机来一个</span>
        </div>
      </div>
    </div>

    <!-- Banner 区域 -->
       <!-- Banner 区域 -->
    <div class="banner">
      <!-- 战术网格背景 -->
      <div class="banner-tactical-grid"></div>
      
      <div class="banner-content">
        <div class="banner-badge">DELTA FORCE</div>
        <h1 class="banner-title">特勤干员，准备行动</h1>
        <p class="banner-desc">全面战场 · 危险行动 · 烽火地带</p>
        <p class="banner-desc">获取曼德尔砖，成功撤离才是胜利。</p>
        
        <!-- 装饰性准星 -->
        <div class="banner-crosshair">+</div>
      </div>
      
      <!-- 右下角战术编号装饰 -->
      <div class="banner-tactical-id">DF-2024</div>
    </div>

    <!-- 体验单推荐 -->
    <div class="recommend-card">
      <div class="recommend-header" style="display: flex; align-items: center;">
        <div class="recommend-icon">
          <van-icon name="star" />
        </div>
        <div class="recommend-title">体验单推荐</div>
      </div>

      <div class="recommend-content">
        <div class="recommend-img experience-img">
          <div class="img-placeholder">
            <span class="ph-title">机密护航</span>
            <span class="ph-sub">88/1000 机密双护</span>
          </div>
        </div>
        <div class="recommend-info">
          <div class="recommend-name">体验单88累计保底1000w</div>
          <div class="recommend-price">¥88</div>
        </div>
      </div>

      <div class="recommend-btn">
        <van-icon name="eye-o" />
        <span>查看详情</span>
      </div>
    </div>

    <!-- 分类 Tab -->
    <div class="category-tabs">
      <div
        v-for="(tab, index) in categories"
        :key="index"
        class="tab-item"
        :class="{ active: activeCategory === tab.key }"
        @click="activeCategory = tab.key"
      >
        <van-icon :name="tab.icon" v-if="tab.icon" />
        <span>{{ tab.name }}</span>
      </div>
    </div>

    <!-- 筛选区域 -->
    <div class="filter-card">
      <div class="search-box">
        <van-icon name="search" />
        <input type="text" placeholder="搜索商品标题..." v-model="keyword" />
      </div>
      <div class="price-filter" style="overflow: hidden;">
        <input type="text" placeholder="最低价" v-model="minPrice" style="min-width: 0; box-sizing: border-box;" />
        <span class="dash">-</span>
        <input type="text" placeholder="最高价" v-model="maxPrice" style="min-width: 0; box-sizing: border-box;" />
      </div>
    </div>

    <!-- 商品列表标题 -->
    <div class="list-header">
      <h2 class="list-title">物资单</h2>
      <span class="list-count">共{{ filteredList.length }}项</span>
    </div>

    <!-- 商品网格列表 -->
    <div class="goods-grid">
      <div
        v-for="(item, index) in filteredList"
        :key="index"
        class="goods-item"
      >
        <div class="goods-img" :class="item.imgClass">
          <div class="img-emoji">{{ item.emoji }}</div>
          <div class="img-overlay">{{ item.label }}</div>
        </div>
        <div class="goods-name">{{ item.name }}</div>
        <div class="goods-price">¥{{ item.price }}</div>
      </div>
    </div>

    <!-- 扫码下单弹窗 -->
    <QrPopup v-model="showQrPopup" />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import QrPopup from '@/components/QrPopup.vue'

const router = useRouter()

const showQrPopup = ref(false)
const activeCategory = ref('wuzidan')
const keyword = ref('')
const minPrice = ref('')
const maxPrice = ref('')

// 随机图标
const randomIconPool = ['dice', 'magic', 'star-o', 'gem-o', 'flower-o', 'gift-o', 'fire-o', 'music-o', 'smile-o', 'sparkles-o', 'medal-o', 'flower', 'gift', 'magic', 'bulb-o', 'photo-o', 'guide-o']
const randomIcon = ref(randomIconPool[Math.floor(Math.random() * randomIconPool.length)])
const changeRandomIcon = () => {
  randomIcon.value = randomIconPool[Math.floor(Math.random() * randomIconPool.length)]
  router.push('/detail')
}

const categories = [
  { name: '全部', key: 'all', icon: 'apps-o' },
  { name: '物资单', key: 'wuzidan', icon: 'gift-o' },
  { name: '小时陪', key: 'xiaoshi', icon: 'clock-o' },
  { name: '趣味单', key: 'quwei', icon: 'medal-o' }
]

const goodsList = ref([
  { name: '核电站1000w', price: 118, priceMax: 128, emoji: '💎', imgClass: 'img-nuclear', label: '核电站' },
  { name: '机密地图488w', price: 66, priceMax: 0, emoji: '💻', imgClass: 'img-map1', label: '机密地图' },
  { name: '机密地图638万', price: 88, priceMax: 0, emoji: '🎯', imgClass: 'img-map2', label: '' },
  { name: '机密地图1000w', price: 108, priceMax: 0, emoji: '🌟', imgClass: 'img-map3', label: '' },
  { name: '机密地图1688w', price: 188, priceMax: 0, emoji: '🌹', imgClass: 'img-map4', label: '' },
  { name: '体验单88累计保底1000w', price: 88, priceMax: 0, emoji: '🎮', imgClass: 'img-experience', label: '机密护航' }
])

const filteredList = computed(() => {
  let list = goodsList.value
  if (keyword.value) {
    list = list.filter(item => item.name.includes(keyword.value))
  }
  if (minPrice.value && Number(minPrice.value)) {
    list = list.filter(item => item.price >= Number(minPrice.value))
  }
  if (maxPrice.value && Number(maxPrice.value)) {
    list = list.filter(item => item.price <= Number(maxPrice.value))
  }
  return list
})
</script>

<style scoped>
.page {
  background: #f7f7f9;
  padding-bottom: 60px;
  overflow-x: hidden;
}

.status-bar {
  height: 44px;
  background: linear-gradient(135deg, #ffc3d1 0%, #f4a6c0 50%, #e899c0 100%);
}

/* 顶部栏 */
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 16px;
  background: linear-gradient(135deg, #ffc3d1 0%, #f4a6c0 50%, #e899c0 100%);
}

.header-brand {
  display: inline-block;
  padding: 6px 14px;
  background: linear-gradient(135deg, #ff6b8a 0%, #ff8fa3 100%);
  color: #fff;
  font-size: 16px;
  font-weight: bold;
  border-radius: 20px;
  box-shadow: 0 2px 8px rgba(255, 107, 138, 0.3);
}

.header-actions {
  display: flex;
  gap: 10px;
  flex-shrink: 0;
}

/* Banner 区域 */
.banner {
  position: relative;
  background: linear-gradient(135deg, #ffc3d1 0%, #f4a6c0 50%, #e899c0 100%);
  padding: 20px 20px 30px;
  overflow: hidden;
}

.banner-bg {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background:
    radial-gradient(circle at 10% 50%, rgba(255, 255, 255, 0.3) 0%, transparent 50%),
    radial-gradient(circle at 90% 20%, rgba(255, 255, 255, 0.2) 0%, transparent 40%);
  pointer-events: none;
}

.banner-content {
  position: relative;
  z-index: 1;
}

.banner-title {
  font-size: 28px;
  font-weight: bold;
  color: #fff;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
  margin-bottom: 10px;
}

.banner-desc {
  font-size: 13px;
  color: rgba(255, 255, 255, 0.85);
  line-height: 1.8;
}

.banner-star {
  position: absolute;
  top: 30px;
  right: 20px;
  font-size: 24px;
  color: rgba(255, 255, 255, 0.6);
}

/* 体验单推荐卡片 */
.recommend-card {
  margin: -15px 16px 16px;
  background: #fff;
  border-radius: 16px;
  padding: 16px;
  box-shadow: 0 4px 16px rgba(232, 153, 192, 0.25);
  position: relative;
  z-index: 2;
}

.recommend-title {
  font-size: 15px;
  font-weight: bold;
  color: #333;
  margin-left: 6px;
}

.recommend-icon {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 22px;
  height: 22px;
  background: linear-gradient(135deg, #ffc857 0%, #ff9a3c 100%);
  color: #fff;
  border-radius: 50%;
  font-size: 12px;
  vertical-align: middle;
}

.recommend-content {
  display: flex;
  align-items: center;
  gap: 14px;
  margin: 14px 0;
}

.recommend-img {
  width: 80px;
  height: 80px;
  border-radius: 12px;
  overflow: hidden;
  flex-shrink: 0;
}

.experience-img .img-placeholder {
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #2d5a27 0%, #4a8c3f 100%);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #fff;
  font-weight: bold;
  padding: 4px;
  text-align: center;
}

.ph-title {
  font-size: 14px;
  margin-bottom: 2px;
}

.ph-sub {
  font-size: 8px;
  opacity: 0.9;
}

.recommend-info {
  flex: 1;
}

.recommend-name {
  font-size: 14px;
  color: #333;
  font-weight: 500;
  margin-bottom: 8px;
  line-height: 1.4;
}

.recommend-price {
  font-size: 20px;
  font-weight: bold;
  color: #ff6b8a;
}

.recommend-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  padding: 10px;
  background: #fef0f3;
  color: #ff6b8a;
  font-size: 13px;
  border-radius: 10px;
  cursor: pointer;
}

/* 分类 Tab */
.category-tabs {
  display: flex;
  gap: 8px;
  padding: 0 16px 14px;
  flex-wrap: wrap;
}

.tab-item {
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 8px 16px;
  background: #fff;
  border-radius: 20px;
  font-size: 13px;
  color: #666;
  transition: all 0.2s;
  border: 1px solid #eee;
}

.tab-item.active {
  background: linear-gradient(135deg, #ff6b8a 0%, #ff8fa3 100%);
  color: #fff;
  border-color: transparent;
  box-shadow: 0 2px 8px rgba(255, 107, 138, 0.3);
}

/* 筛选卡片 */
.filter-card {
  margin: 0 16px 16px;
  background: #fff;
  border-radius: 14px;
  padding: 14px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.search-box {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 14px;
  background: #f7f7f9;
  border-radius: 20px;
  margin-bottom: 12px;
  color: #999;
}

.search-box input {
  flex: 1;
  border: none;
  background: none;
  outline: none;
  font-size: 13px;
  color: #333;
}

.search-box input::placeholder {
  color: #bbb;
}

.price-filter {
  display: flex;
  align-items: center;
  gap: 8px;
}

.price-filter input {
  flex: 1;
  padding: 10px 14px;
  border: none;
  background: #f7f7f9;
  border-radius: 20px;
  font-size: 13px;
  color: #333;
  outline: none;
  text-align: center;
}

.price-filter input::placeholder {
  color: #bbb;
}

.price-filter .dash {
  color: #ccc;
}

/* 列表标题 */
.list-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 16px 12px;
}

.list-title {
  font-size: 16px;
  font-weight: bold;
  color: #333;
}

.list-count {
  font-size: 12px;
  color: #999;
}

/* 商品网格 */
.goods-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  padding: 0 16px;
}

.goods-item {
  background: #fff;
  border-radius: 14px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.goods-img {
  width: 100%;
  height: 140px;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.img-nuclear {
  background: linear-gradient(135deg, #1a1a2e 0%, #3d3d5c 100%);
}

.img-map1 {
  background: linear-gradient(135deg, #4a2c6a 0%, #7b4a9e 100%);
}

.img-map2 {
  background: linear-gradient(135deg, #6b3a3a 0%, #b85c5c 100%);
}

.img-map3 {
  background: linear-gradient(135deg, #3d5a3d 0%, #6b8c3d 100%);
}

.img-map4 {
  background: linear-gradient(135deg, #2c3e50 0%, #4a6572 100%);
}

.img-experience {
  background: linear-gradient(135deg, #2d5a27 0%, #4a8c3f 100%);
}

.img-emoji {
  font-size: 48px;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.3));
}

.img-overlay {
  position: absolute;
  bottom: 8px;
  left: 8px;
  right: 8px;
  font-size: 13px;
  font-weight: bold;
  color: #fff;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.5);
  line-height: 1.3;
}

.goods-name {
  font-size: 13px;
  color: #333;
  padding: 10px 12px 4px;
  line-height: 1.4;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.goods-price {
  font-size: 15px;
  font-weight: bold;
  color: #ff6b8a;
  padding: 0 12px 12px;
}

/* Banner 区域 - 三角洲战术风格 */
.banner {
  position: relative;
  /* 深色战术背景：混合了深灰和暗绿色 */
  background: linear-gradient(135deg, #1a1c20 0%, #2c3e28 50%, #1a1c20 100%);
  padding: 24px 20px 34px;
  overflow: hidden;
  color: #fff;
  border-bottom: 2px solid #4a5d45; /* 底部加一条战术绿边 */
}

/* 战术网格背景纹理 */
.banner-tactical-grid {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  /* 使用重复线性渐变模拟网格 */
  background-image: 
    linear-gradient(rgba(74, 93, 69, 0.1) 1px, transparent 1px),
    linear-gradient(90deg, rgba(74, 93, 69, 0.1) 1px, transparent 1px);
  background-size: 20px 20px;
  pointer-events: none;
  z-index: 0;
}

/* 增加一个斜切的装饰条，增加速度感 */
.banner::after {
  content: '';
  position: absolute;
  top: 0;
  right: -20px;
  width: 100px;
  height: 100%;
  background: rgba(255, 165, 0, 0.05); /* 淡淡的警示橙 */
  transform: skewX(-20deg);
  pointer-events: none;
}

.banner-content {
  position: relative;
  z-index: 1;
}

/* 顶部小标签 */
.banner-badge {
  display: inline-block;
  font-size: 10px;
  font-weight: 900;
  letter-spacing: 2px;
  color: #ffa500; /* 警示橙 */
  border: 1px solid #ffa500;
  padding: 2px 6px;
  margin-bottom: 8px;
  text-transform: uppercase;
  opacity: 0.9;
}

.banner-title {
  font-size: 26px;
  font-weight: 800;
  color: #f0f0f0;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5);
  margin-bottom: 12px;
  font-family: 'Arial Black', sans-serif; /* 尝试使用更粗的字体 */
  letter-spacing: 1px;
}

.banner-desc {
  font-size: 13px;
  color: #aebfad; /* 浅军绿色文字 */
  line-height: 1.6;
  font-weight: 500;
}

/* 装饰性准星 */
.banner-crosshair {
  position: absolute;
  top: 20px;
  right: 20px;
  font-size: 40px;
  color: rgba(255, 165, 0, 0.3); /* 半透明橙色准星 */
  font-weight: bold;
  line-height: 1;
  pointer-events: none;
}

/* 右下角战术编号 */
.banner-tactical-id {
  position: absolute;
  bottom: 10px;
  right: 15px;
  font-size: 10px;
  color: rgba(255, 255, 255, 0.2);
  font-family: monospace;
  letter-spacing: 1px;
}
/* 建议修改：顶部状态栏和Header背景，使其与Banner融合或形成对比 */
.status-bar {
  height: 44px;
  background: #1a1c20; /* 深色背景 */
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 16px;
  background: #1a1c20; /* 深色背景 */
  border-bottom: 1px solid #333;
}

.header-brand {
  display: inline-block;
  padding: 6px 14px;
  background: #333; /* 深灰底 */
  color: #ffa500; /* 橙色字 */
  font-size: 16px;
  font-weight: bold;
  border-radius: 4px; /* 直角或微圆角，更硬朗 */
  border: 1px solid #444;
}

.icon-btn {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 6px 12px;
  background: #2c2c2c;
  border-radius: 4px;
  color: #ccc;
  font-size: 12px;
  white-space: nowrap;
}

.icon-btn span {
  font-size: 12px;
}
</style>
