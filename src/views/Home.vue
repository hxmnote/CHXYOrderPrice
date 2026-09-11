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

    <!-- Banner 轮播图 -->
    <div class="banner-swiper">
      <van-swipe :autoplay="3000" indicator-color="#7c6aff">
        <van-swipe-item v-for="(img, i) in bannerImages" :key="i">
          <img :src="img" class="banner-img" />
        </van-swipe-item>
      </van-swipe>
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
          <img :src="ty1" class="ty-img" />
        </div>
        <div class="recommend-info">
          <div class="recommend-name">体验单78累计保底650w</div>
          <div class="recommend-price">¥78</div>
        </div>
      </div>

      <div class="recommend-btn"
      @click="router.push({ path: '/detail', query: { detail: 'ty1' } })"
      >
        <van-icon name="eye-o" />
        <span >查看详情</span>
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
      <h2 class="list-title">{{ currentCategoryName }}</h2>
      <span class="list-count">共{{ filteredList.length }}项</span>
    </div>

    <!-- 商品网格列表 -->
    <div class="goods-wrap">
      <div class="goods-bg"></div>
      <div class="goods-grid">
        <div
          v-for="(item, index) in filteredList"
          :key="index"
          class="goods-item"
          @click="router.push({ path: '/detail', query: { detail: item.detail, type: item.type } })"
        >
          <div class="goods-img">
            <img :src="item.url" class="goods-img-inner" />
          </div>
          <div class="goods-info">
            <div class="goods-name">{{ item.name }}</div>
            <div class="goods-price">¥{{ item.price }}</div>
          </div>
        </div>
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
import img1 from '@/source/11.png'
import img2 from '@/source/22.png'
import img3 from '@/source/33.png'
// 2. 导入商品列表图片
import qw1 from '@/source/qw1.jpg'
import qw2 from '@/source/qw2.jpg'
import qw3 from '@/source/qw3.png'
import qw4 from '@/source/qw4.jpg'
import ty1 from '@/source/ty1.png'

const router = useRouter()

const bannerImages = [img1, img2, img3]

const showQrPopup = ref(false)
const activeCategory = ref('all')
const keyword = ref('')
const minPrice = ref('')
const maxPrice = ref('')

// 随机图标
const randomIconPool = ['dice', 'magic', 'star-o', 'gem-o', 'flower-o', 'gift-o', 'fire-o', 'music-o', 'smile-o', 'sparkles-o', 'medal-o', 'flower', 'gift', 'magic', 'bulb-o', 'photo-o', 'guide-o']
const randomIcon = ref(randomIconPool[Math.floor(Math.random() * randomIconPool.length)])
const changeRandomIcon = () => {
  randomIcon.value = randomIconPool[Math.floor(Math.random() * randomIconPool.length)]
  const randomItem = goodsList.value[Math.floor(Math.random() * goodsList.value.length)]
  router.push({ path: '/detail', query: { detail: randomItem.detail } })
}

const categories = [
  { name: '全部', key: 'all', icon: 'apps-o' },
  { name: '物资单', key: 'wuzidan', icon: 'gift-o' },
  { name: '小时陪', key: 'xiaoshi', icon: 'clock-o' },
  { name: '趣味单', key: 'quwei', icon: 'medal-o' }
]

// 秋季机密趣味单 - 4个订单
const funOrders = [
  {
    name: '肥的流油',
    tag: '红物质保底',
    options: [{ sub: '', price: 268, guarantee: '1200w' }],
    rules: '板板带出的红色物资（含撤离失败），均以对局结束后交易行实时售价为准。在此基础上，额外追加该物品价值 × 3 倍的保底收益，累积上限5000w。',
    note: '（航天包核心区全卡，其他图板需要开卡+10元保2红2金卡）',
    type: '3_1'
  },
  {
    name: '穷得吃土',
    tag: '摸红减半',
    options: [{ sub: '', price: 268, guarantee: '1200w' }],
    rules: '每摸到一个红，当局带出物资总价除以一次2。栗子：当局总总价800万的货，兜里有3个红！实际带出那就是800万 ÷ 2 = 400万，÷2 再 ÷2 = 100万保底！',
    note: '（打手以漏补卡为由未开卡，下一局撤离直接除以一次2，发现打手藏红，3倍退款。板板出红需给打手过手查看，防止开局带入）',
    type: '3_2'
  },
  {
    name: '冤弟我怕疼',
    tag: '止疼片挑战',
    options: [
      { sub: '累积带出88粒DVE止疼片', price: 328, guarantee: '1988w' },
      { sub: '累积带出166粒DEV止疼片', price: 588, guarantee: '3000w' }
    ],
    rules: '止痛4/5，算4粒，对局只算撤离成功！出红或者50w以上房卡止抵消5粒，此单打手会与板板抢！不为别的，因为打手痛怕！需要大量止疼药~',
    note: '板板可以趁打手打架的时候偷藏，也可以酷酷狂吃！打手也可以尾随板板寻找！',
    type: '3_3'
  },
  {
    name: '绝地大逃杀',
    tag: '存活时间挑战',
    options: [
      { sub: '1分钟 * 50w', price: 198, guarantee: '800w' },
      { sub: '1分钟 * 100w', price: 298, guarantee: '1200w' }
    ],
    rules: '地图航天，第一局板板每存活时间一分钟增加对应版本保底，打手可以捣乱暴露板板位置，打手死亡板板可以不救。',
    note: '开局板板可以要求打手开过点卡和房卡，板板不用撤离！',
    type: '3_4'
  }
]

// type 1 物资单 2 小时陪 3 趣味单
const goodsList = ref([
  { name: '肥的流油', price: 268, priceMax: 128, url: qw1,  label: '' ,type: 3, detail: '3_1'},
  { name: '穷得吃土', price: 268, priceMax: 0, url: qw2,  label: '' ,type: 3, detail: '3_2'},
  { name: '兄弟我怕疼', price: 328, priceMax: 0, url: qw3,  label: '' ,type: 3, detail: '3_3'},
  { name: '绝地大逃杀', price: 198, priceMax: 0, url: qw4,  label: '' ,type: 3, detail: '3_4'},
  { name: '物资单', price: 666, priceMax: 0, url: qw4, label: '' ,type: 1, detail: '1_1'},
  { name: '小时陪', price: 999, priceMax: 0, url: qw4,  label: '' ,type: 2, detail: '2_1'},
])

// 分类 key 对应商品 type
const categoryTypeMap = {
  all: null,
  wuzidan: 1,
  xiaoshi: 2,
  quwei: 3
}

// 当前分类名称
const currentCategoryName = computed(() => {
  const tab = categories.find(c => c.key === activeCategory.value)
  return tab ? tab.name : '全部'
})

const filteredList = computed(() => {
  let list = goodsList.value
  // 按分类筛选
  const type = categoryTypeMap[activeCategory.value]
  if (type !== null) {
    list = list.filter(item => item.type === type)
  }
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
/* === 浅色动漫卡通风主题 ===
   底色 #f5f7fa / 卡片白 / 主色 紫蓝 #7c6aff / 副色 粉 #ff6baf
   圆角大 12-16px / 柔和阴影
   ========================== */

.page {
  background: #f5f7fa;
  padding-bottom: 60px;
  overflow-x: hidden;
}

.status-bar {
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
}

/* 顶部栏 */
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 16px;
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
}

.header-brand {
  display: inline-block;
  padding: 6px 14px;
  background: rgba(255, 255, 255, 0.25);
  color: #fff;
  font-size: 16px;
  font-weight: bold;
  border-radius: 20px;
  backdrop-filter: blur(4px);
}

.header-actions {
  display: flex;
  gap: 10px;
  flex-shrink: 0;
}

.icon-btn {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 6px 12px;
  background: rgba(255, 255, 255, 0.25);
  border-radius: 20px;
  color: #fff;
  font-size: 12px;
  white-space: nowrap;
  transition: all 0.2s;
}

.icon-btn:hover {
  background: rgba(255, 255, 255, 0.4);
}

.icon-btn span {
  font-size: 12px;
}

/* Banner 轮播图 */
.banner-swiper {
  margin: 0 0 16px;
}

.banner-swiper .van-swipe {
  border-radius: 0;
}

.banner-img {
  width: 100%;
  height: 200px;
  object-fit: cover;
  display: block;
}

.ty-img{
    width: 100%;
  height: 100%;
  object-fit: cover; /* 关键属性：保持比例并填满容器，多余部分裁剪 */
  display: block;
}

/* 体验单推荐卡片 */
.recommend-card {
  /* margin: -15px 16px 16px; */
  margin: 15px;
  background: #fff;
  border-radius: 16px;
  padding: 16px;
  box-shadow: 0 4px 20px rgba(124, 106, 255, 0.12);
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
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
  color: #fff;
  border-radius: 50%;
  font-size: 12px;
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
  background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
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
  color: #7c6aff;
}

.recommend-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  padding: 10px;
  background: #f0edff;
  color: #7c6aff;
  font-size: 13px;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.2s;
}

.recommend-btn:hover {
  background: #e4deff;
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
  color: #888;
  transition: all 0.2s;
  border: 1px solid #eee;
}

.tab-item.active {
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
  color: #fff;
  border-color: transparent;
  box-shadow: 0 2px 12px rgba(124, 106, 255, 0.3);
}

/* 筛选卡片 */
.filter-card {
  margin: 0 16px 16px;
  background: #fff;
  border-radius: 14px;
  padding: 14px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.04);
}

.search-box {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 14px;
  background: #f5f7fa;
  border-radius: 20px;
  margin-bottom: 12px;
  color: #bbb;
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
  color: #ccc;
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
  background: #f5f7fa;
  border-radius: 20px;
  font-size: 13px;
  color: #333;
  outline: none;
  text-align: center;
}

.price-filter input::placeholder {
  color: #ccc;
}

.price-filter input:focus {
  background: #f0edff;
}

.price-filter .dash {
  color: #ccc;
}

/* 趣味单订单卡片 */
.order-list {
  padding: 0 16px 16px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.order-card {
  position: relative;
  background: #fff;
  border-radius: 16px;
  padding: 40px 16px 16px;
  box-shadow: 0 4px 20px rgba(124, 106, 255, 0.12);
  border: 2px solid #f0edff;
  transition: all 0.2s;
  cursor: pointer;
}

.order-card:hover {
  box-shadow: 0 6px 28px rgba(124, 106, 255, 0.2);
  transform: translateY(-2px);
}

/* 左上角名称 */
.order-corner-tl {
  position: absolute;
  top: 12px;
  left: 16px;
  font-size: 18px;
  font-weight: 900;
  color: #7c6aff;
  text-shadow: 1px 1px 0 #f0edff;
}

/* 右上角标签 */
.order-corner-tr {
  position: absolute;
  top: 12px;
  right: 16px;
  font-size: 11px;
  color: #a78bfa;
  background: #f0edff;
  padding: 3px 10px;
  border-radius: 10px;
}

.order-price-row {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-bottom: 10px;
  padding: 8px 12px;
  background: linear-gradient(135deg, #f0edff 0%, #e8e4ff 100%);
  border-radius: 10px;
}

.order-sub {
  font-size: 12px;
  color: #7c6aff;
  font-weight: bold;
  flex: 1;
}

.order-price {
  font-size: 22px;
  font-weight: 900;
  color: #7c6aff;
}

.order-guarantee {
  font-size: 11px;
  color: #a78bfa;
  background: #fff;
  padding: 3px 8px;
  border-radius: 8px;
}

.order-rules {
  margin-top: 12px;
  padding-top: 12px;
  border-top: 1px dashed #e0d8ff;
}

.rules-title {
  display: inline-block;
  font-size: 12px;
  font-weight: bold;
  color: #fff;
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
  padding: 3px 12px;
  border-radius: 8px;
  margin-bottom: 8px;
  letter-spacing: 2px;
}

.rules-text {
  font-size: 13px;
  color: #666;
  line-height: 1.7;
}

.rules-note {
  font-size: 12px;
  color: #999;
  line-height: 1.6;
  margin-top: 6px;
  background: #f8f7ff;
  padding: 8px 10px;
  border-radius: 8px;
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
  color: #aaa;
  background: #f0edff;
  padding: 2px 10px;
  border-radius: 10px;
  color: #7c6aff;
}

/* 商品网格容器 */
.goods-wrap {
  position: relative;
  padding: 4px 0 16px;
}

.goods-bg {
  position: absolute;
  inset: 0;
  background-image: url('@/source/33.png');
  background-size: cover;
  background-position: center;
  opacity: 0.08;
  pointer-events: none;
  border-radius: 20px;
}

.goods-grid {
  position: relative;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  padding: 0 16px;
  z-index: 1;
}

.goods-item {
  background: #fff;
  border-radius: 14px;
  overflow: hidden;
  border: 1.5px solid #c4b5fd;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.04);
  transition: all 0.2s;
}

.goods-item:hover {
  box-shadow: 0 4px 20px rgba(124, 106, 255, 0.15);
  transform: translateY(-2px);
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
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.img-map1 {
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
}

.img-map2 {
  background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
}

.img-map3 {
  background: linear-gradient(135deg, #43e97b 0%, #38f9d7 100%);
}

.img-map4 {
  background: linear-gradient(135deg, #fa709a 0%, #fee140 100%);
}

.img-experience {
  background: linear-gradient(135deg, #30cfd0 0%, #330867 100%);
}

.img-emoji {
  font-size: 48px;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.15));
}

.img-overlay {
  position: absolute;
  bottom: 8px;
  left: 8px;
  right: 8px;
  font-size: 13px;
  font-weight: bold;
  color: #fff;
  text-shadow: 1px 1px 4px rgba(0, 0, 0, 0.3);
  line-height: 1.3;
}

.goods-info {
  padding: 10px 12px 12px;
  background: linear-gradient(135deg, #faf9ff 0%, #f0edff 100%);
}

.goods-name {
  font-size: 15px;
  font-weight: bold;
  color: #333;
  line-height: 1.4;
  margin-bottom: 6px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.goods-price {
  font-size: 18px;
  font-weight: 900;
  color: #7c6aff;
  text-shadow: 0 1px 4px rgba(124, 106, 255, 0.15);
}

.goods-img-inner {
  width: 100%;
  height: 100%;
  object-fit: cover; /* 关键属性：保持比例并填满容器，多余部分裁剪 */
  display: block;
}
</style>
