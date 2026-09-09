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
    <div class="product-card">
      <div class="product-tag">
        <van-icon name="medal-o" />
        <span>小时陪</span>
      </div>
      <h1 class="product-title">机密地图 · 极速撤离</h1>
      <div class="product-price-row">
        <span class="price-symbol">¥</span>
        <span class="price-num">98</span>
        <span class="price-sep">~</span>
        <span class="price-symbol">¥</span>
        <span class="price-num">158</span>
        <span class="price-unit">/h</span>
      </div>

      <!-- 商品详情 -->
      <div class="detail-section">
        <div class="section-title">商品详情</div>
        <div class="section-content">
          <p class="desc-item">🎯 资深干员带队，熟悉全地图撤离路线</p>
          <p class="desc-item">🛡️ 专业战术配合，包全卡保底撤离</p>
          <p class="desc-item">💎 曼德尔砖保底，物资满载而归</p>
          <p class="desc-item">⚡ 极速响应，下单即刻开战</p>
        </div>
      </div>

      <!-- 价位选项 -->
      <div class="detail-section">
        <div class="section-title">价位选项</div>
        <div class="price-options">
          <div
            v-for="(opt, idx) in priceOptions"
            :key="idx"
            class="price-option"
            :class="{ active: selectedOption === idx }"
            @click="selectedOption = idx"
          >
            <div class="option-header">
              <span class="option-name">{{ opt.name }}</span>
              <span class="option-price">¥{{ opt.price.toFixed(2) }}</span>
            </div>
            <div class="option-desc">{{ opt.desc }}</div>
            <div class="option-badge" v-if="opt.tag">{{ opt.tag }}</div>
          </div>
        </div>
      </div>

      <!-- 详情图片 -->
      <div class="detail-section">
        <div class="section-title">详情图片</div>
        <div class="detail-img-box">
          <div class="tactical-table">
            <div class="table-header">
              <div class="table-title">
                <span class="title-left">晨辉星游</span>
                <span class="title-main">价目表</span>
              </div>
            </div>

            <div class="table-body">
              <div class="table-col">
                <div class="col-label">
                  <van-icon name="user-o" />
                  <span>单陪</span>
                </div>
                <div class="col-list">
                  <div v-for="(item, i) in singleList" :key="i" class="col-row">
                    <span class="row-name">{{ item.name }}</span>
                    <span class="row-val">{{ item.price }}/{{ item.unit }}</span>
                  </div>
                </div>
              </div>

              <div class="table-col">
                <div class="col-label">
                  <van-icon name="friends-o" />
                  <span>双陪</span>
                </div>
                <div class="col-list">
                  <div v-for="(item, i) in doubleList" :key="i" class="col-row">
                    <span class="row-name">{{ item.name }}</span>
                    <span class="row-val">{{ item.price }}</span>
                  </div>
                </div>
              </div>

              <div class="table-col">
                <div class="col-label">
                  <van-icon name="flag-o" />
                  <span>保底</span>
                </div>
                <div class="col-list">
                  <div v-for="(item, i) in guaranteeList" :key="i" class="col-row">
                    <span class="row-name">{{ item.name }}</span>
                    <span class="row-val">{{ item.price }}</span>
                  </div>
                </div>
              </div>
            </div>

            <!-- 底部装饰 -->
            <div class="table-footer">
              <div class="footer-deco-left"></div>
              <span class="footer-text">DELTA FORCE · 晨辉星游</span>
              <div class="footer-deco-right"></div>
            </div>
          </div>
        </div>
      </div>
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
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import QrPopup from '@/components/QrPopup.vue'

const router = useRouter()
const showQrPopup = ref(false)

const selectedOption = ref(1)

const priceOptions = [
  { name: '高手双陪', price: 98, desc: '包全卡，保底四撤一', tag: '性价比之选' },
  { name: '顶尖双陪', price: 128, desc: '包全卡，保底三撤一', tag: '热门' },
  { name: '魔王双陪', price: 158, desc: '包全卡，保底三撤一', tag: '职业级' }
]

const singleList = [
  { name: '技术单陪', price: '¥48', unit: '/h' },
  { name: '高手单陪', price: '¥55', unit: '/h' },
  { name: '顶尖单陪', price: '¥69', unit: '/h' },
  { name: '魔王单陪', price: '¥88', unit: '/h' }
]

const doubleList = [
  { name: '高手双陪', price: '¥98/h' },
  { name: '顶尖双陪', price: '¥128/h' },
  { name: '魔王双陪', price: '¥158/h' }
]

const guaranteeList = [
  { name: '高手保底', price: '4撤1' },
  { name: '顶尖保底', price: '3撤1' },
  { name: '魔王保底', price: '3撤1' }
]

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
  background: #f7f7f9;
  padding-bottom: 100px;
}

/* 状态栏和导航 */
.status-bar {
  height: 44px;
  background: linear-gradient(135deg, #1a1c20 0%, #2c3e28 100%);
}

.nav-bar {
  padding: 10px 16px;
  background: linear-gradient(135deg, #1a1c20 0%, #2c3e28 100%);
}

.nav-back {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 8px 18px;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.15);
  border-radius: 20px;
  color: #f0f0f0;
  font-size: 14px;
  cursor: pointer;
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
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
}

.product-tag {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 4px 12px;
  background: #fef0f3;
  border-radius: 12px;
  font-size: 12px;
  color: #ff6b8a;
  margin-bottom: 14px;
}

.product-title {
  font-size: 22px;
  font-weight: bold;
  color: #222;
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
  color: #ff6b8a;
}

.price-num {
  font-size: 30px;
  font-weight: 900;
  color: #ff6b8a;
}

.price-sep {
  font-size: 22px;
  color: #bbb;
  font-weight: bold;
  margin: 0 4px;
}

.price-unit {
  font-size: 13px;
  color: #999;
  margin-left: 4px;
}

/* 通用 section */
.detail-section {
  margin-top: 24px;
}

.section-title {
  font-size: 15px;
  font-weight: bold;
  color: #333;
  margin-bottom: 12px;
  padding-left: 10px;
  border-left: 3px solid #ff6b8a;
}

.section-content {
  background: #f7f7f9;
  border-radius: 12px;
  padding: 14px 16px;
}

.desc-item {
  font-size: 14px;
  color: #555;
  line-height: 1.8;
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
  border-radius: 12px;
  transition: all 0.2s;
  cursor: pointer;
}

.price-option.active {
  border-color: #ff6b8a;
  background: #fef6f8;
  box-shadow: 0 2px 8px rgba(255, 107, 138, 0.15);
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
  color: #ff6b8a;
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
  padding: 2px 8px;
  background: linear-gradient(135deg, #ff6b8a 0%, #ff8fa3 100%);
  color: #fff;
  font-size: 10px;
  border-radius: 8px;
  font-weight: bold;
}

/* 详情图片 - 战术风格价目表 */
.detail-img-box {
  padding: 10px;
  background: linear-gradient(135deg, #1a1c20 0%, #2c3e28 100%);
  border-radius: 12px;
  overflow: hidden;
}

.tactical-table {
  background: #fef0f3;
  border-radius: 8px;
  padding: 12px;
  position: relative;
  overflow: hidden;
}

.table-header {
  text-align: center;
  margin-bottom: 12px;
  padding-bottom: 8px;
  border-bottom: 2px dashed rgba(0, 0, 0, 0.15);
}

.table-title {
  display: flex;
  align-items: baseline;
  justify-content: center;
  gap: 4px;
}

.title-left {
  font-size: 12px;
  color: #ff6b8a;
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
  background: rgba(255, 255, 255, 0.8);
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
  color: #ff6b8a;
  margin-bottom: 8px;
  padding-bottom: 6px;
  border-bottom: 1px solid rgba(255, 107, 138, 0.2);
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
  color: #555;
}

.row-val {
  color: #ff6b8a;
  font-weight: bold;
}

.table-footer {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-top: 12px;
  padding-top: 8px;
  border-top: 1px dashed rgba(0, 0, 0, 0.15);
}

.footer-deco-left,
.footer-deco-right {
  width: 20px;
  height: 2px;
  background: linear-gradient(90deg, transparent, #ff6b8a, transparent);
}

.footer-text {
  font-size: 10px;
  color: #999;
  letter-spacing: 1px;
}

/* 底部下单栏 */
.bottom-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background: #fff;
  padding: 12px 20px 24px;
  box-shadow: 0 -4px 16px rgba(0, 0, 0, 0.08);
  text-align: center;
}

.bottom-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  padding: 14px;
  background: linear-gradient(135deg, #ff6b8a 0%, #ff8fa3 100%);
  border-radius: 28px;
  color: #fff;
  font-size: 16px;
  font-weight: bold;
  box-shadow: 0 4px 16px rgba(255, 107, 138, 0.3);
  cursor: pointer;
}

.bottom-btn .van-icon {
  font-size: 20px;
}

.bottom-sub {
  margin-top: 8px;
  font-size: 12px;
  color: #999;
}
</style>
