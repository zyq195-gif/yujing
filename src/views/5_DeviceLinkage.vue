<template>
  <div class="view-page linkage-page">
    <div class="page-hero">
      <div>
        <p class="eyebrow">System Linkage Demo</p>
        <h2 class="page-title">联动模块与设备控制</h2>
        <p class="page-subtitle">
          展示预警触发后的人、车、设备和生产系统的联动流程，强调前端演示效果和交互反馈。
        </p>
      </div>
      <div class="hero-actions">
        <button class="ghost-btn" @click="simulateLinkage">
          {{ simulated ? '已触发演示联动' : '模拟联动触发' }}
        </button>
      </div>
    </div>

    <LinkageCommandBoard />

    <section class="top-grid">
      <article class="panel strategy-panel">
        <div class="panel-head">
          <div>
            <p class="panel-kicker">联动策略</p>
            <h3>自动处置链路</h3>
          </div>
          <span class="badge warning">橙色及以上</span>
        </div>
        <div class="strategy-list">
          <div
            v-for="item in strategies"
            :key="item.title"
            class="strategy-item"
            :class="{ active: selectedMode === item.key }"
            @click="selectedMode = item.key"
          >
            <div class="strategy-icon">{{ item.icon }}</div>
            <div>
              <strong>{{ item.title }}</strong>
              <p>{{ item.desc }}</p>
            </div>
          </div>
        </div>
      </article>

      <article class="panel event-panel">
        <div class="panel-head">
          <div>
            <p class="panel-kicker">联动结果</p>
            <h3>{{ currentMode.title }}</h3>
          </div>
          <span class="badge neutral">{{ currentMode.tag }}</span>
        </div>
        <div class="event-card">
          <p class="event-summary">{{ currentMode.summary }}</p>
          <div class="event-tags">
            <span v-for="tag in currentMode.tags" :key="tag">{{ tag }}</span>
          </div>
          <div class="event-metrics">
            <div v-for="metric in currentMode.metrics" :key="metric.label">
              <span>{{ metric.label }}</span>
              <strong>{{ metric.value }}</strong>
            </div>
          </div>
        </div>
      </article>
    </section>

    <section class="device-section panel">
      <div class="panel-head">
        <div>
          <p class="panel-kicker">设备状态</p>
          <h3>多系统联动控制台</h3>
        </div>
        <span class="badge success">在线率 87%</span>
      </div>
      <div class="device-grid">
        <div v-for="device in devices" :key="device.name" class="device-card" :class="device.state">
          <div class="device-top">
            <div>
              <span class="device-type">{{ device.type }}</span>
              <strong>{{ device.name }}</strong>
            </div>
            <span class="device-state">{{ device.status }}</span>
          </div>
          <p>{{ device.note }}</p>
          <div class="device-actions">
            <button
              v-for="action in device.actions"
              :key="action"
              :class="{ danger: action.includes('停') || action.includes('关闭') || action.includes('撤离') }"
            >
              {{ action }}
            </button>
          </div>
        </div>
      </div>
    </section>

    <section class="bottom-grid">
      <article class="panel flow-panel">
        <div class="panel-head">
          <div>
            <p class="panel-kicker">业务流程</p>
            <h3>无人驾驶与采掘设备安全联动</h3>
          </div>
        </div>
        <div class="flow-steps">
          <div v-for="step in flowSteps" :key="step.title" class="flow-step">
            <div class="step-no">{{ step.no }}</div>
            <div class="step-body">
              <strong>{{ step.title }}</strong>
              <p>{{ step.desc }}</p>
            </div>
          </div>
        </div>
      </article>

      <article class="panel log-panel">
        <div class="panel-head">
          <div>
            <p class="panel-kicker">联动日志</p>
            <h3>控制台回显</h3>
          </div>
        </div>
        <div class="log-list">
          <div v-for="log in logs" :key="log.time + log.text" class="log-item">
            <span>{{ log.time }}</span>
            <p>{{ log.text }}</p>
          </div>
        </div>
      </article>
    </section>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue';
import LinkageCommandBoard from '../components/LinkageCommandBoard.vue';

const simulated = ref(false);
const selectedMode = ref('autonomous');

const strategies = [
  {
    key: 'autonomous',
    icon: 'A',
    title: '无人驾驶道路联动',
    desc: '针对橙色以上道路预警，自动推送标准化指令到无人驾驶调度平台。'
  },
  {
    key: 'mining',
    icon: 'B',
    title: '采掘设备风险干预',
    desc: '设备接近风险区域时，触发关注、限速、停产待命或撤离等分级控制。'
  },
  {
    key: 'site',
    icon: 'C',
    title: '现场广播与门禁联动',
    desc: '联动广播、道闸、门禁、摄像头和巡检终端执行区域封控与疏散。'
  }
];

const modeMap = {
  autonomous: {
    title: '无人驾驶系统安全联动',
    tag: 'API 推送演示',
    summary: '系统将预警位置、等级和影响范围以标准指令形式推送至车辆调度平台，展示绕行、减速和暂停通行的控制效果。',
    tags: ['动态路径规划', '危险区域绕行', '减速慢行', '暂停通过'],
    metrics: [
      { label: '联动车辆', value: '12 辆' },
      { label: '改道任务', value: '4 条' },
      { label: '平均响应', value: '2.4 秒' }
    ]
  },
  mining: {
    title: '采掘设备安全预警',
    tag: '设备干预演示',
    summary: '将采掘设备定位与预警区域叠加分析，触发调度端对设备进行分级管控与人工确认。',
    tags: ['临近风险提醒', '限速行驶', '停产待命', '立即撤离'],
    metrics: [
      { label: '高风险设备', value: '3 台' },
      { label: '待确认指令', value: '2 条' },
      { label: '视频联查', value: '5 路' }
    ]
  },
  site: {
    title: '现场系统联动控制',
    tag: '区域封控演示',
    summary: '联动广播、门禁、警示灯与视频系统，展示区域封控、人员疏散和现场复核的统一执行界面。',
    tags: ['广播通知', '门禁闭锁', '道闸落杆', '视频轮巡'],
    metrics: [
      { label: '封控卡口', value: '2 处' },
      { label: '广播区域', value: '4 个' },
      { label: '巡检终端', value: '6 台' }
    ]
  }
};

const devices = [
  {
    type: '无人驾驶',
    name: '北帮运输车队调度接口',
    status: '已联动',
    note: '已下发绕行指令，危险路段限制通行。',
    state: 'online',
    actions: ['查看路径', '暂停通行']
  },
  {
    type: '采掘设备',
    name: 'EX-07 电铲作业面',
    status: '关注中',
    note: '已推送风险提醒，等待调度员确认是否限速停机。',
    state: 'warning',
    actions: ['风险提示', '限速作业', '停产待命']
  },
  {
    type: '安防广播',
    name: '北帮广播与声光报警',
    status: '在线',
    note: '广播模板已切换为橙色预警疏散通告。',
    state: 'online',
    actions: ['测试播报', '启动广播']
  },
  {
    type: '门禁道闸',
    name: '1 号运输道路卡口',
    status: '执行中',
    note: '道闸落杆，门禁转为只出不进模式。',
    state: 'warning',
    actions: ['关闭入口', '恢复通行']
  },
  {
    type: '视频巡检',
    name: '边坡巡检摄像头组',
    status: '在线',
    note: '已切换到高风险区域轮巡画面。',
    state: 'online',
    actions: ['轮巡预览', '锁定画面']
  },
  {
    type: '移动终端',
    name: '现场巡检 APP',
    status: '离线 1 台',
    note: '5 台在线，1 台待重连，仅做 demo 状态展示。',
    state: 'offline',
    actions: ['发送提醒', '重新连接']
  }
];

const flowSteps = [
  { no: '01', title: '识别风险区域', desc: '系统将实时预警区域与道路、设备、作业点位叠加分析。' },
  { no: '02', title: '生成标准指令', desc: '形成带有位置、等级、影响范围的标准化联动指令。' },
  { no: '03', title: '推送调度平台', desc: '向无人驾驶平台、生产调度系统和安防系统同步推送。' },
  { no: '04', title: '回传执行状态', desc: '通过设备回执、视频画面和移动端上报形成闭环展示。' }
];

const logs = [
  { time: '16:42:10', text: '橙色预警触发，北帮运输道路进入联动控制模式。' },
  { time: '16:42:12', text: '向无人驾驶平台发送绕行任务，4 辆车重新规划路径。' },
  { time: '16:42:15', text: '向 EX-07 电铲和 2 台卡车发送风险提示，等待调度确认。' },
  { time: '16:42:18', text: '广播、道闸和视频轮巡策略已切换至应急模板。' }
];

const currentMode = computed(() => modeMap[selectedMode.value]);

const simulateLinkage = () => {
  simulated.value = !simulated.value;
};
</script>

<style scoped>
.linkage-page {
  display: flex;
  flex-direction: column;
  gap: 18px;
  color: #16325c;
}

.page-hero,
.panel {
  background: rgba(255, 255, 255, 0.94);
  border: 1px solid rgba(28, 61, 144, 0.08);
  border-radius: 20px;
  box-shadow: 0 18px 40px rgba(28, 61, 144, 0.08);
}

.page-hero {
  display: flex;
  justify-content: space-between;
  gap: 24px;
  padding: 24px 28px;
  background:
    linear-gradient(135deg, rgba(24, 120, 196, 0.08), rgba(44, 181, 125, 0.04)),
    rgba(255, 255, 255, 0.94);
}

.eyebrow,
.panel-kicker {
  font-size: 12px;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: #6b86b4;
}

.page-title {
  margin-top: 6px;
  font-size: 32px;
  color: #1c3d90;
}

.page-subtitle {
  margin-top: 10px;
  max-width: 720px;
  line-height: 1.7;
  color: #5d7192;
}

.hero-actions {
  display: flex;
  align-items: flex-start;
}

.ghost-btn {
  border: 1px solid rgba(28, 61, 144, 0.16);
  border-radius: 999px;
  padding: 10px 18px;
  font-size: 13px;
  color: #1c3d90;
  background: #f8fbff;
  cursor: pointer;
  transition: transform 0.2s ease;
}

.ghost-btn:hover {
  transform: translateY(-1px);
}

.top-grid,
.bottom-grid {
  display: grid;
  gap: 18px;
}

.top-grid {
  grid-template-columns: 0.95fr 1.05fr;
}

.bottom-grid {
  grid-template-columns: 1.1fr 0.9fr;
}

.panel {
  padding: 20px;
}

.panel-head {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 12px;
  margin-bottom: 18px;
}

.panel-head h3 {
  margin-top: 4px;
  font-size: 20px;
  color: #17376f;
}

.badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 82px;
  padding: 6px 12px;
  border-radius: 999px;
  font-size: 12px;
  font-weight: 600;
}

.badge.warning {
  background: #fff0df;
  color: #d9781f;
}

.badge.success {
  background: #e7f8ee;
  color: #1d8e5a;
}

.badge.neutral {
  background: #edf4ff;
  color: #4470b8;
}

.strategy-list,
.flow-steps,
.log-list {
  display: grid;
  gap: 12px;
}

.strategy-item {
  display: grid;
  grid-template-columns: 48px 1fr;
  gap: 14px;
  align-items: center;
  padding: 14px 16px;
  border-radius: 16px;
  background: #f8fbff;
  border: 1px solid transparent;
  cursor: pointer;
  transition: border-color 0.2s ease, transform 0.2s ease, box-shadow 0.2s ease;
}

.strategy-item.active {
  border-color: rgba(28, 100, 242, 0.24);
  box-shadow: 0 12px 24px rgba(28, 100, 242, 0.12);
  transform: translateY(-1px);
}

.strategy-icon,
.step-no {
  width: 48px;
  height: 48px;
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #e8f1ff, #d7e7ff);
  color: #1c3d90;
  font-weight: 700;
}

.strategy-item p,
.event-summary,
.device-card p,
.step-body p,
.log-item p {
  margin-top: 6px;
  line-height: 1.6;
  color: #5f728f;
  font-size: 13px;
}

.event-card {
  padding: 18px;
  border-radius: 18px;
  background:
    radial-gradient(circle at top right, rgba(33, 120, 255, 0.12), transparent 30%),
    #f7faff;
  border: 1px solid rgba(58, 103, 187, 0.08);
}

.event-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 14px;
}

.event-tags span {
  padding: 6px 12px;
  border-radius: 999px;
  background: #fff;
  font-size: 12px;
  color: #54709b;
  border: 1px solid rgba(28, 61, 144, 0.08);
}

.event-metrics {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
  margin-top: 16px;
}

.event-metrics div {
  padding: 14px 16px;
  border-radius: 16px;
  background: #fff;
}

.event-metrics span,
.device-type,
.log-item span {
  font-size: 12px;
  color: #7890b1;
}

.event-metrics strong,
.device-top strong {
  display: block;
  margin-top: 8px;
  font-size: 16px;
}

.device-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 14px;
}

.device-card {
  padding: 16px;
  border-radius: 18px;
  border: 1px solid rgba(28, 61, 144, 0.08);
  background: #f8fbff;
}

.device-card.online {
  background: linear-gradient(180deg, #f5fffa, #eefaf5);
}

.device-card.warning {
  background: linear-gradient(180deg, #fff8ef, #fff4e6);
}

.device-card.offline {
  background: linear-gradient(180deg, #f4f6f9, #eceff4);
}

.device-top {
  display: flex;
  justify-content: space-between;
  gap: 12px;
}

.device-state {
  white-space: nowrap;
  font-size: 12px;
  font-weight: 700;
  color: #476897;
}

.device-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-top: 14px;
}

.device-actions button {
  border: 1px solid rgba(28, 61, 144, 0.12);
  border-radius: 999px;
  padding: 7px 12px;
  font-size: 12px;
  color: #1c3d90;
  background: #fff;
  cursor: pointer;
}

.device-actions button.danger {
  color: #fff;
  background: #d96d3b;
  border-color: #d96d3b;
}

.flow-step {
  display: grid;
  grid-template-columns: 48px 1fr;
  gap: 14px;
  align-items: start;
  padding: 14px 16px;
  border-radius: 16px;
  background: #f8fbff;
}

.step-body strong {
  color: #17376f;
}

.log-item {
  padding: 14px 16px;
  border-radius: 16px;
  background: #f8fbff;
  border-left: 3px solid #79a5ea;
}

@media (max-width: 1200px) {
  .top-grid,
  .bottom-grid,
  .device-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .page-hero {
    flex-direction: column;
  }

  .event-metrics {
    grid-template-columns: 1fr;
  }

  .page-title {
    font-size: 26px;
  }
}
</style>
