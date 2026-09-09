<template>
  <van-popup :show="modelValue" position="center" round :style="{ padding: '24px 20px 28px', width: '320px' }" @update:show="val => emit('update:modelValue', val)">
    <div class="qr-popup">
      <div class="qr-popup-header">
        <div class="qr-popup-title">
          <van-icon name="star" color="#ffb6c1" />
          <span>扫码下单</span>
          <van-icon name="star" color="#ffb6c1" />
        </div>
        <div class="qr-popup-close" @click="close">
          <van-icon name="cross" />
        </div>
      </div>
      <div class="qr-popup-body">
        <div class="qr-code-box">
          <div class="qr-code">
            <div class="qr-pattern">
              <div class="qr-corner qr-tl"></div>
              <div class="qr-corner qr-tr"></div>
              <div class="qr-corner qr-bl"></div>
              <div class="qr-center-logo">
                <van-icon name="chat-o" />
              </div>
            </div>
          </div>
        </div>
        <div class="qr-popup-tip">微信扫一扫，客服姐姐带你下单哦 ✨</div>
      </div>
    </div>
  </van-popup>
</template>

<script setup>
const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['update:modelValue'])

const close = () => {
  emit('update:modelValue', false)
}
</script>

<style scoped>
.qr-popup {
  position: relative;
}

.qr-popup-header {
  position: relative;
  text-align: center;
  padding-bottom: 16px;
}

.qr-popup-title {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  font-size: 20px;
  font-weight: bold;
  color: #333;
}

.qr-popup-close {
  position: absolute;
  top: 0;
  right: 0;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  border: 2px solid #ffb6c1;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #ff8fa3;
  font-size: 18px;
  cursor: pointer;
  background: #fff;
}

.qr-popup-body {
  text-align: center;
}

.qr-code-box {
  background: #fff;
  border: 1px solid #f0f0f0;
  border-radius: 16px;
  padding: 20px;
  margin-bottom: 20px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.05);
}

.qr-code {
  width: 220px;
  height: 220px;
  margin: 0 auto;
  background:
    linear-gradient(90deg, #000 50%, transparent 50%),
    linear-gradient(0deg, #000 50%, transparent 50%),
    repeating-linear-gradient(90deg, #000 0 8px, transparent 8px 16px),
    repeating-linear-gradient(0deg, #000 0 8px, transparent 8px 16px);
  background-size: 2px 2px, 2px 2px, 10px 10px, 10px 10px;
  position: relative;
  border-radius: 4px;
}

.qr-pattern {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}

.qr-corner {
  position: absolute;
  width: 44px;
  height: 44px;
  border: 8px solid #000;
  background: #fff;
}

.qr-corner::after {
  content: '';
  position: absolute;
  top: 8px;
  left: 8px;
  right: 8px;
  bottom: 8px;
  background: #000;
}

.qr-tl { top: 0; left: 0; }
.qr-tr { top: 0; right: 0; }
.qr-bl { bottom: 0; left: 0; }

.qr-center-logo {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 48px;
  height: 48px;
  background: #fff;
  border: 2px solid #000;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  color: #000;
}

.qr-popup-tip {
  font-size: 15px;
  color: #888;
}
</style>
