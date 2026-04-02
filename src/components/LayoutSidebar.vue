<template>
  <aside class="sidebar-container">
    <div class="side-nav-header">滑坡风险预警子系统</div>
    <nav class="nav-list">
      <div
        v-for="item in navItems"
        :key="item.id"
        :class="['nav-item', { active: activeId === item.id }]"
        @click="$emit('navigate', item.id)"
      >
        <span class="icon">
          <i :class="item.icon"></i> </span>
        <span class="name">{{ item.name }}</span>
      </div>
    </nav>
  </aside>
</template>

<script setup>
import '@fortawesome/fontawesome-free/css/all.css'; // 引入图标库，如 FontAwesome

defineProps(['activeId']);
defineEmits(['navigate']);

const navItems = [
  { id: 'dashboard', name: '滑坡预警综合看板', icon: 'fas fa-chart-line' },
  { id: 'workorder', name: '风险闭环管理', icon: 'fas fa-tasks' },
  { id: 'decision', name: '智能决策与专家库', icon: 'fas fa-brain' },
  { id: 'emergency', name: '应急预案与指挥', icon: 'fas fa-headset' },
  { id: 'linkage', name: '联动模块与设备控制', icon: 'fas fa-cogs' },
  { id: 'evaluation', name: '效能评估与报表', icon: 'fas fa-file-contract' }
];
</script>

<style scoped>
.sidebar-container {
  width: 260px; /* 1. 增加宽度，更稳重 */
  background: rgba(255, 255, 255, 0.92); /* 2. 降低透明度，更扎实 */
  border-right: 1px solid rgba(225, 233, 241, 1);
  backdrop-filter: blur(8px);
  display: flex;
  flex-direction: column;
}

.side-nav-header {
  display: flex;
  justify-content: center;
  align-items: center;
  /* 核心修改 1：将顶边距加大到 32px，底边距缩小到 16px，利用内外边距的差值将文字整体向下推 */
  padding: 32px 20px 16px;
  font-size: 19px;
  font-weight: 900;
  color: #1c3d90;
  letter-spacing: 1.5px;
  margin-bottom: 15px;
  background: transparent;
  /* 核心修改 2：将底边框的宽度从 1px 加粗到 2px，让分界线更明显 */
  border-bottom: 2px solid rgba(225, 233, 241, 1);
}

.nav-list {
  display: flex;
  flex-direction: column;
  gap: 8px; /* 4. 增加项间距，呼吸感 */
}

/* 5. 导航项：调整布局、去掉外边距 */
.nav-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 14px 24px;
  font-size: 14px;
  color: #333;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  border-left: 4px solid transparent; /* 6. 预留左指示条空间 */
  /* margin: 0 12px;  <- 移除外边距 */
}

/* 7. 图标样式 */
.icon {
  font-size: 16px;
  width: 24px; /* 固定图标区域宽度 */
  text-align: center;
}

/* 悬浮样式：浅蓝背景 */
.nav-item:hover {
  background: rgba(133, 198, 241, 0.2);
  color: #1c3d90;
}

/* 选中样式：深蓝渐变高亮 + 左指示条 */
.nav-item.active {
  background: linear-gradient(to right, #C3E4FD 20%, #D6EDFE 100%); /* 渐变范围 */
  color: #1c3d90;
  font-weight: bold;
  border-left-color: #1c3d90;
}
</style>