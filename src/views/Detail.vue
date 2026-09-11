<template>
  <div class="detail-page">
    <!-- 顶部返回 -->
    <div class="status-bar"></div>
    <div class="nav-bar">
      <div class="nav-back" @click="goBack">
        <van-icon name="arrow-left" />
        <span>返回首页</span>
      </div>
    </div>

    <!-- 商品卡片 -->
    <div class="product-card" v-if="order">
      <!-- 标签 -->
      <div class="product-tag" v-if="order.tag">
        <van-icon name="medal-o" />
        <span>{{ order.tag }}</span>
      </div>

      <!-- 标题 -->
      <h1 class="product-title" v-if="order.name">{{ order.name }}</h1>

      <!-- 展示图片 -->
      <div class="detail-section" v-if="order.url">
        <div class="section-title">图片详情</div>
        <div class="show-image-box">
          <img :src="order.url" class="show-image" />
        </div>
      </div>

      <!-- 价位选项 -->
      <div class="detail-section" v-if="order.options && order.options.length">
        <div class="section-title">价位选项</div>
        <div class="price-options">
          <div
            v-for="(opt, idx) in order.options"
            :key="idx"
            class="price-option"
            :class="{ active: selectedOption === idx }"
            @click="selectedOption = idx"
          >
            <div class="option-header">
              <span class="option-name">{{ opt.sub || order.name }}</span>
              <span class="option-price">¥{{ opt.price }}</span>
            </div>
            <div class="option-desc">基础保底 {{ opt.guarantee }}</div>
          </div>
        </div>
      </div>

      <!-- 规则详情 -->
      <div class="detail-section" v-if="order.rules">
        <div class="section-title">规则详情</div>
        <div class="section-content">
          <p class="desc-item">{{ order.rules }}</p>
          <p class="desc-item note" v-if="order.note">{{ order.note }}</p>
        </div>
      </div>
    </div>

    <!-- 未匹配到数据 -->
    <div class="product-card" v-else>
      <div class="empty-tip">未找到该订单信息</div>
    </div>

    <!-- 老板须知 -->
    <div class="boss-notice" v-if="order && order.sourceType === 3">
      <div class="notice-header">
        <van-icon name="warning-o" />
        <span>老板须知</span>
      </div>
      <div class="notice-body">
        <p class="notice-item"><span class="notice-num">一、</span>以上趣味单没有双倒机制，板板与打手都需要尽力搬离。</p>
        <p class="notice-item"><span class="notice-num">二、</span>为了保证趣味性，板板有什么疑问下单前与客服沟通好，然后游戏按照规则进行。选图上，板板可与打手商量沟通，必要时要求板板放过打手。来自打手的哭泣：呜呜~</p>
      </div>
      <div class="notice-footer">以上最终解释权归晨辉星游电竞所有</div>
    </div>

    <!-- 底部下单栏 -->
    <div class="bottom-bar">
      <div class="bottom-btn" @click="placeOrder">
        <van-icon name="gold-coin-o" />
        <span>立刻下单</span>
      </div>
      <div class="bottom-sub">公众号：晨辉星游</div>
    </div>

    <!-- 扫码下单弹窗 -->
    <QrPopup v-model="showQrPopup" />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import QrPopup from '@/components/QrPopup.vue'
import imgTy from '@/source/ty1.jpg'


const router = useRouter()
const route = useRoute()
const showQrPopup = ref(false)

const selectedOption = ref(0)

const funOrders = [
  {
    name: '肥的流油',
    tag: '红物质保底',
    options: [{ sub: '', price: 268, guarantee: '1200w' }],
    rules: '板板带出的红色物资（含撤离失败），均以对局结束后交易行实时售价为准。在此基础上，额外追加该物品价值 × 3 倍的保底收益，累积上限5000w。',
    note: '（航天包核心区全卡，其他图板需要开卡+10元保2红2金卡）',
    sourceType: 3,
    type: '3_1'
  },
  {
    name: '穷得吃土',
    tag: '摸红减半',
    options: [{ sub: '', price: 268, guarantee: '1200w' }],
    rules: '每摸到一个红，当局带出物资总价除以一次2。栗子：当局总总价800万的货，兜里有3个红！实际带出那就是800万 ÷ 2 = 400万，÷2 再 ÷2 = 100万保底！',
    note: '（打手以漏补卡为由未开卡，下一局撤离直接除以一次2，发现打手藏红，3倍退款。板板出红需给打手过手查看，防止开局带入）',
    sourceType: 3,
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
    sourceType: 3,
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
    sourceType: 3,
    type: '3_4'
  },
  {
    name: '保底体验区',
    tag: '肥肥得吃',
    options: [],
    rules: '',
    note: '',
    type: 'ty1',
    sourceType: 99,
    url:imgTy,

  }
]

// 根据 query.detail 匹配 funOrders 中的 type 字段
const order = computed(() => {
  const detail = route.query.detail
  if (!detail) return funOrders[0]
  return funOrders.find(item => item.type === detail) || null
})

const goBack = () => {
  router.push('/')
}

const placeOrder = () => {
  showQrPopup.value = true
}
</script>

<style scoped>
.detail-page {
  min-height: 100vh;
  background: #f5f7fa;
  padding-bottom: 100px;
}

/* 状态栏和导航 */
.status-bar {
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
}

.nav-bar {
  padding: 10px 16px;
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
}

.nav-back {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 8px 18px;
  background: rgba(255, 255, 255, 0.25);
  border-radius: 20px;
  color: #fff;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.2s;
  backdrop-filter: blur(4px);
}

.nav-back:hover {
  background: rgba(255, 255, 255, 0.4);
}

.nav-back .van-icon {
  font-size: 16px;
}

/* 商品卡片 */
.product-card {
  margin: -1px 12px 0;
  background: #fff;
  border-radius: 0 0 20px 20px;
  padding: 20px 18px 24px;
  box-shadow: 0 4px 20px rgba(124, 106, 255, 0.08);
}

.product-tag {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 4px 12px;
  background: #f0edff;
  border-radius: 12px;
  font-size: 12px;
  color: #7c6aff;
  margin-bottom: 14px;
}

.product-title {
  font-size: 22px;
  font-weight: bold;
  color: #333;
  margin-bottom: 10px;
}

.product-price-row {
  display: flex;
  align-items: baseline;
  gap: 2px;
}

.price-symbol {
  font-size: 16px;
  font-weight: bold;
  color: #7c6aff;
}

.price-num {
  font-size: 30px;
  font-weight: 900;
  color: #7c6aff;
}

.price-sep {
  font-size: 22px;
  color: #ccc;
  font-weight: bold;
  margin: 0 4px;
}

.price-unit {
  font-size: 13px;
  color: #aaa;
  margin-left: 4px;
}

/* 通用 section */
.detail-section {
  margin-top: 24px;
}

/* 展示图片 */
.show-image-box {
  border-radius: 14px;
  overflow: hidden;
  box-shadow: 0 4px 16px rgba(124, 106, 255, 0.1);
}

.show-image {
  width: 100%;
  display: block;
}

.section-title {
  font-size: 15px;
  font-weight: bold;
  color: #333;
  margin-bottom: 12px;
  padding-left: 10px;
  border-left: 3px solid #7c6aff;
}

.section-content {
  background: #f5f7fa;
  border-radius: 12px;
  padding: 14px 16px;
}

.desc-item {
  font-size: 14px;
  color: #555;
  line-height: 1.8;
}

.desc-item.note {
  font-size: 13px;
  color: #999;
  margin-top: 8px;
  padding: 8px 12px;
  background: #f8f7ff;
  border-radius: 8px;
}

.empty-tip {
  text-align: center;
  padding: 40px 0;
  color: #aaa;
  font-size: 14px;
}

/* 价位选项 */
.price-options {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.price-option {
  position: relative;
  padding: 14px 16px;
  background: #fff;
  border: 1px solid #eee;
  border-radius: 14px;
  transition: all 0.2s;
  cursor: pointer;
}

.price-option.active {
  border-color: #7c6aff;
  background: #f0edff;
  box-shadow: 0 2px 12px rgba(124, 106, 255, 0.15);
}

.option-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 6px;
}

.option-name {
  font-size: 15px;
  font-weight: bold;
  color: #333;
}

.option-price {
  font-size: 18px;
  font-weight: bold;
  color: #7c6aff;
}

.option-desc {
  font-size: 12px;
  color: #999;
}

.option-badge {
  position: absolute;
  top: 0;
  right: 10px;
  transform: translateY(-50%);
  padding: 2px 10px;
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
  color: #fff;
  font-size: 10px;
  border-radius: 10px;
  font-weight: bold;
}

/* 详情图片 - 价目表 */
.detail-img-box {
  padding: 10px;
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
  border-radius: 14px;
  overflow: hidden;
}

.tactical-table {
  background: #fff;
  border-radius: 10px;
  padding: 12px;
  position: relative;
  overflow: hidden;
}

.table-header {
  text-align: center;
  margin-bottom: 12px;
  padding-bottom: 8px;
  border-bottom: 2px dashed rgba(124, 106, 255, 0.2);
}

.table-title {
  display: flex;
  align-items: baseline;
  justify-content: center;
  gap: 4px;
}

.title-left {
  font-size: 12px;
  color: #7c6aff;
  font-weight: bold;
}

.title-main {
  font-size: 22px;
  font-weight: 900;
  color: #333;
  letter-spacing: 2px;
}

.table-body {
  display: flex;
  gap: 8px;
}

.table-col {
  flex: 1;
  background: #f8f7ff;
  border-radius: 8px;
  padding: 8px 6px;
}

.col-label {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 3px;
  font-size: 12px;
  font-weight: bold;
  color: #7c6aff;
  margin-bottom: 8px;
  padding-bottom: 6px;
  border-bottom: 1px solid rgba(124, 106, 255, 0.15);
}

.col-label .van-icon {
  font-size: 14px;
}

.col-list {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.col-row {
  display: flex;
  justify-content: space-between;
  font-size: 10px;
  padding: 2px 0;
}

.row-name {
  color: #888;
}

.row-val {
  color: #7c6aff;
  font-weight: bold;
}

.table-footer {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-top: 12px;
  padding-top: 8px;
  border-top: 1px dashed rgba(124, 106, 255, 0.2);
}

.footer-deco-left,
.footer-deco-right {
  width: 20px;
  height: 2px;
  background: linear-gradient(90deg, transparent, #7c6aff, transparent);
}

.footer-text {
  font-size: 10px;
  color: #aaa;
  letter-spacing: 1px;
}

/* 老板须知 */
.boss-notice {
  margin: 16px 12px 0;
  background: #fff;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 4px 20px rgba(124, 106, 255, 0.08);
  border: 1px solid #f0edff;
}

.notice-header {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 12px 16px;
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
  color: #fff;
  font-size: 15px;
  font-weight: bold;
  letter-spacing: 1px;
}

.notice-header .van-icon {
  font-size: 18px;
}

.notice-body {
  padding: 14px 16px;
}

.notice-item {
  font-size: 13px;
  color: #555;
  line-height: 1.8;
  margin-bottom: 10px;
}

.notice-item:last-child {
  margin-bottom: 0;
}

.notice-num {
  font-weight: bold;
  color: #7c6aff;
}

.notice-footer {
  text-align: center;
  font-size: 11px;
  color: #aaa;
  padding: 8px 16px 14px;
  border-top: 1px dashed #eee;
  margin-top: 4px;
}

/* 底部下单栏 */
.bottom-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: #fff;
  padding: 12px 20px 24px;
  box-shadow: 0 -4px 20px rgba(124, 106, 255, 0.08);
  text-align: center;
}

.bottom-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  padding: 14px;
  background: linear-gradient(135deg, #7c6aff 0%, #a78bfa 100%);
  border-radius: 28px;
  color: #fff;
  font-size: 16px;
  font-weight: bold;
  box-shadow: 0 4px 16px rgba(124, 106, 255, 0.3);
  cursor: pointer;
  transition: all 0.2s;
}

.bottom-btn:hover {
  box-shadow: 0 6px 24px rgba(124, 106, 255, 0.4);
  transform: translateY(-1px);
}

.bottom-btn .van-icon {
  font-size: 20px;
}

.bottom-sub {
  margin-top: 8px;
  font-size: 12px;
  color: #aaa;
}
</style>
