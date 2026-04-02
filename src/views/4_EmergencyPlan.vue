<template>
  <div class="view-page emergency-page">
    <div class="page-hero">
      <div>
        <p class="eyebrow">Emergency Command Demo</p>
        <h2 class="page-title">应急预案与指挥控制</h2>
        <p class="page-subtitle">
          根据预警等级自动匹配处置预案，并通过可视化看板展示人员、车辆和设备的调度路线。
        </p>
      </div>
      <div class="hero-actions">
        <button class="ghost-btn">查看预案库</button>
        <button class="primary-btn" @click="launchPlan">
          {{ launched ? '已启动演示预案' : '一键启动预案' }}
        </button>
      </div>
    </div>

    <section class="panel command-board">
      <div class="panel-head">
        <div>
          <p class="panel-kicker">指挥看板</p>
          <h3>人员与车辆预案规划路线</h3>
        </div>
        <span class="badge warning">橙色预警 / II 级响应</span>
      </div>

      <div class="command-layout">
        <div class="map-board">
          <div ref="threeContainer" class="three-viewport"></div>
          <div class="map-overlay warning-chip">橙色预警</div>
          <div class="map-overlay controls-chip">左键平移 / 按住 Ctrl+左键旋转 / 滚轮缩放</div>
        </div>

        <div class="board-sidebar">
          <div class="sidebar-summary">
            <div class="summary-card">
              <span>预警区域</span>
              <strong>北帮 3 号台阶至运输道路</strong>
            </div>
            <div class="summary-card">
              <span>在途对象</span>
              <strong>7 人员 / 3 车辆 / 2 设备</strong>
            </div>
          </div>

          <div class="board-list">
            <div class="board-table table-head">
              <span>预警等级</span>
              <span>预警区域</span>
              <span>人员、车辆编号</span>
              <span>距离</span>
            </div>
            <div
              v-for="item in sortedCommandUnits"
              :key="item.code"
              class="board-table table-row"
              :class="{ selected: selectedUnitCode === item.code }"
              @click="focusUnit(item.code)"
            >
              <div class="unit-cell">
                <strong>{{ item.level }}</strong>
              </div>
              <div class="unit-cell">
                <strong>{{ item.area }}</strong>
              </div>
              <div class="unit-cell">
                <strong>{{ item.code }}</strong>
              </div>
              <div class="unit-cell">
                <strong>{{ item.distance }}</strong>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section class="overview-grid">
      <article class="panel featured-plan">
        <div class="panel-head featured-plan-head">
          <div class="panel-head-actions">
            <button class="ghost-btn">查看预案库</button>
            <button class="primary-btn" @click="launchPlan">
              {{ launched ? '已启动演示预案' : '一键启动预案' }}
            </button>
            <span class="badge warning">橙色预警</span>
          </div>
          <div>
            <p class="panel-kicker">当前匹配结果</p>
            <h3>北帮边坡滑移橙色预警处置预案</h3>
          </div>
          <span class="badge warning">橙色预警</span>
        </div>
        <div class="featured-plan-layout">
          <div>
            <div class="plan-meta">
              <div>
                <span>触发区域</span>
                <strong>北帮 3 号台阶至运输道路</strong>
              </div>
              <div>
                <span>推荐响应级别</span>
                <strong>II 级响应</strong>
              </div>
              <div>
                <span>影响对象</span>
                <strong>道路、排土设备、现场人员</strong>
              </div>
            </div>
            <div class="action-flow">
              <div
                v-for="item in responseActions"
                :key="item.id"
                class="flow-item"
                :class="item.state"
              >
                <div class="flow-index">{{ item.id }}</div>
                <div class="flow-content">
                  <strong>{{ item.title }}</strong>
                  <p>{{ item.desc }}</p>
                </div>
                <span class="flow-state">{{ item.label }}</span>
              </div>
            </div>
          </div>

          <aside class="route-summary">
            <div class="route-summary-head">
              <p class="panel-kicker">路线规划说明</p>
              <h4>预案调度文字描述</h4>
            </div>
            <div class="route-summary-list">
              <div v-for="item in routePlans" :key="item.title" class="route-summary-item">
                <strong>{{ item.title }}</strong>
                <p>{{ item.text }}</p>
              </div>
            </div>
          </aside>
        </div>
      </article>
    </section>

    <section class="detail-grid">
      <article class="panel progress-panel">
        <div class="panel-head">
          <div>
            <p class="panel-kicker">处置进度</p>
            <h3>指令执行闭环</h3>
          </div>
          <span class="badge success">完成率 67%</span>
        </div>
        <div class="timeline">
          <div
            v-for="task in tasks"
            :key="task.name"
            class="timeline-item"
            :class="task.state"
          >
            <div class="timeline-track"></div>
            <div class="timeline-body">
              <div class="timeline-top">
                <strong>{{ task.name }}</strong>
                <span>{{ task.owner }}</span>
              </div>
              <p>{{ task.note }}</p>
            </div>
          </div>
        </div>
      </article>

      <article class="panel resource-panel">
        <div class="panel-head">
          <div>
            <p class="panel-kicker">资源状态</p>
            <h3>人员与设备调度</h3>
          </div>
          <span class="badge neutral">实时示意</span>
        </div>
        <div class="resource-cards">
          <div v-for="card in resources" :key="card.title" class="resource-card">
            <span>{{ card.title }}</span>
            <strong>{{ card.value }}</strong>
            <p>{{ card.note }}</p>
          </div>
        </div>
      </article>

      <article class="panel feedback-panel">
        <div class="panel-head">
          <div>
            <p class="panel-kicker">现场反馈</p>
            <h3>移动端回传演示</h3>
          </div>
          <button class="ghost-btn small" @click="toggleFeed">
            {{ feedExpanded ? '收起详情' : '展开详情' }}
          </button>
        </div>
        <div class="feedback-list">
          <div v-for="item in feedbacks" :key="item.unit" class="feedback-item">
            <div>
              <strong>{{ item.unit }}</strong>
              <p>{{ item.text }}</p>
            </div>
            <span class="badge" :class="item.level">{{ item.status }}</span>
          </div>
        </div>
        <div v-if="feedExpanded" class="feedback-extra">
          <div class="snapshot-card">
            <span>现场图片回传</span>
            <div class="snapshot-placeholder">图片/视频占位</div>
          </div>
          <div class="snapshot-card">
            <span>电子档案归集</span>
            <div class="archive-lines">
              <p>已归档指令 12 条</p>
              <p>已回传素材 6 份</p>
              <p>待确认隐患 1 项</p>
            </div>
          </div>
        </div>
      </article>
    </section>
  </div>
</template>

<script setup>
import { computed, onMounted, onUnmounted, ref } from 'vue';
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

const launched = ref(false);
const feedExpanded = ref(true);
const threeContainer = ref(null);
const selectedUnitCode = ref('P-07');

let renderer;
let scene;
let camera;
let controls;
let animationId;
let pointSprites = new Map();

const commandUnits = [
  { code: 'P-07', type: '人员', level: '橙色', area: '北帮 3 号台阶', position: '撤离路线 A', route: '前往集结点 A', distance: '85 m', status: '撤离中' },
  { code: 'P-12', type: '人员', level: '橙色', area: '运输道路东侧', position: '撤离路线 B', route: '前往集结点 B', distance: '132 m', status: '已确认' },
  { code: 'P-19', type: '巡检人员', level: '橙色', area: '排土平台边缘', position: '巡检复核线', route: '向安全观测点移动', distance: '176 m', status: '复核中' },
  { code: 'T-05', type: '运输车辆', level: '橙色', area: '北帮道路', position: '车辆绕行路线', route: '绕行 2 号辅路', distance: '240 m', status: '改道中' },
  { code: 'T-11', type: '应急车辆', level: '橙色', area: '应急车库', position: '救援通道', route: '前往北帮入口', distance: '315 m', status: '前往中' },
  { code: 'EX-07', type: '电铲设备', level: '橙色', area: '作业面边界', position: '设备撤离路线', route: '退出高风险区域', distance: '96 m', status: '待停机' }
];

const sortedCommandUnits = computed(() =>
  [...commandUnits].sort((a, b) => Number.parseFloat(a.distance) - Number.parseFloat(b.distance))
);

const mapUnits = [
  { code: 'P-07', type: 'person', color: '#67d7ff', x: 32, z: 31, blink: true },
  { code: 'P-12', type: 'person', color: '#67d7ff', x: 20, z: 20 },
  { code: 'P-19', type: 'person', color: '#67d7ff', x: 9, z: 10 },
  { code: 'T-05', type: 'vehicle', color: '#ffd166', x: 43, z: 16 },
  { code: 'T-11', type: 'vehicle', color: '#ffd166', x: 56, z: 2 },
  { code: 'EX-07', type: 'equipment', color: '#ff8c69', x: 16, z: 34 }
];

const mapRoutes = [
  {
    label: '人员撤离路线',
    color: '#59d8ff',
    points: [
      [32, 31],
      [22, 24],
      [10, 18],
      [-2, 12]
    ],
    labelAt: [-1, 14]
  },
  {
    label: '车辆绕行路线',
    color: '#ffd166',
    points: [
      [43, 16],
      [56, 14],
      [66, 8],
      [76, -2]
    ],
    labelAt: [60, 12]
  },
  {
    label: '设备撤离路线',
    color: '#ff8c69',
    points: [
      [16, 34],
      [10, 42],
      [2, 48],
      [-8, 54]
    ],
    labelAt: [3, 46]
  }
];

commandUnits.push(
  { code: 'P-23', type: '人员', level: '橙色', area: '北帮作业面', position: '撤离路线一段', route: '向北侧安全点移动', distance: '108 m', status: '撤离中' },
  { code: 'P-28', type: '人员', level: '橙色', area: '运输道路西侧', position: '撤离路线二段', route: '向安全通道转移', distance: '154 m', status: '待确认' },
  { code: 'P-31', type: '安全员', level: '橙色', area: '预警区边缘', position: '现场管控线', route: '引导人员撤离', distance: '64 m', status: '值守中' },
  { code: 'P-36', type: '巡检人员', level: '无风险', area: '南侧观测点', position: '日常巡查线', route: '维持原观察路线', distance: '610 m', status: '正常巡查' },
  { code: 'T-22', type: '运输车辆', level: '无风险', area: '南侧运输道路', position: '常规通行路线', route: '按既定线路通行', distance: '420 m', status: '运行正常' },
  { code: 'EQ-14', type: '排水设备', level: '无风险', area: '东南排水区', position: '设备待命位', route: '保持原地待命', distance: '560 m', status: '状态正常' }
);

mapUnits.push(
  { code: 'P-23', type: 'person', color: '#67d7ff', x: 26, z: 26 },
  { code: 'P-28', type: 'person', color: '#67d7ff', x: 14, z: 17 },
  { code: 'P-31', type: 'person', color: '#67d7ff', x: 30, z: 19 },
  { code: 'P-36', type: 'person', color: '#79e2b0', x: -42, z: -26 },
  { code: 'T-22', type: 'vehicle', color: '#79e2b0', x: -56, z: -8 },
  { code: 'EQ-14', type: 'equipment', color: '#79e2b0', x: -34, z: -38 }
);

const responseActions = [
  { id: '01', title: '自动通知应急小组', desc: '企业微信、电话和广播同步推送预警详情与集结指令。', state: 'done', label: '已通知' },
  { id: '02', title: '生成撤离工单', desc: '向北帮作业面和值守卡口自动派发撤离与封控任务。', state: 'active', label: '执行中' },
  { id: '03', title: '联动设备与资源', desc: '调度排水泵、应急车辆与视频巡检资源前往指定位置。', state: 'pending', label: '待联动' }
];

const routePlans = [
  { title: '人员疏散路线', text: '北帮 3 号台阶作业人员沿人员撤离线分段外撤，北侧作业面人员优先向安全通道移动，运输道路附近人员同步退出预警影响带。' },
  { title: '车辆绕行路线', text: '运输车辆 T-05、T-11 由 2 号辅路和北侧救援通道改道，避开橙色预警区及其影响扩散边界。' },
  { title: '设备撤离路线', text: 'EX-07 电铲退出作业面边界后沿设备撤离路线回收到安全待命区，等待调度中心确认复工。' },
  { title: '封控与接应节点', text: '1 号卡口实施封控，北帮入口保留应急车辆通道，巡检人员在安全观测点完成复核并回传现场状态。' }
];

const tasks = [
  { name: '危险区域停工与清场', owner: '调度中心 / 已签收', note: '北帮道路作业设备已停机，人员撤离至 2 号集结点。', state: 'done' },
  { name: '封控运输道路与门禁', owner: '安环部 / 执行中', note: '1 号卡口正在设置拦截，预计 4 分钟完成。', state: 'active' },
  { name: '现场复核与无人机巡查', owner: '巡检组 / 待出发', note: '等待气象窗口，准备进行二次影像复核。', state: 'pending' }
];

const resources = [
  { title: '到位人员', value: '18 / 22', note: '4 人正在赶赴集结点' },
  { title: '应急车辆', value: '3 辆', note: '2 辆已到达北帮道路入口' },
  { title: '排水与照明', value: '5 套', note: '设备联动指令已生成' },
  { title: '现场视频', value: '6 路', note: '其中 1 路切换至移动巡检视角' }
];

const feedbacks = [
  { unit: '现场巡检组', text: '已确认裂缝区域周边无滞留人员，等待下一步巡检任务。', status: '已回传', level: 'success' },
  { unit: '道路封控组', text: '卡口拦截杆已落下，运输车辆开始分流绕行。', status: '处理中', level: 'warning' },
  { unit: '调度指挥台', text: '电子档案持续归集，准备生成本次应急过程记录。', status: '跟踪中', level: 'neutral' }
];

const launchPlan = () => {
  launched.value = !launched.value;
};

const toggleFeed = () => {
  feedExpanded.value = !feedExpanded.value;
};

const focusUnit = (code) => {
  selectedUnitCode.value = code;
  const unit = mapUnits.find((item) => item.code === code);
  if (unit && controls) {
    controls.target.set(unit.x, getTerrainHeight(unit.x, unit.z), unit.z);
    camera.position.set(unit.x + 14, 86, unit.z + 12);
  }
  updateSelectionVisuals();
};

const createSpotTexture = () => {
  const canvas = document.createElement('canvas');
  canvas.width = 256;
  canvas.height = 256;
  const ctx = canvas.getContext('2d');
  ctx.beginPath();
  ctx.arc(128, 128, 120, 0, Math.PI * 2);

  const gradient = ctx.createRadialGradient(128, 128, 0, 128, 128, 120);
  gradient.addColorStop(0, 'rgba(255,255,255,0.4)');
  gradient.addColorStop(0.8, 'rgba(255,255,255,0.3)');
  gradient.addColorStop(1, 'rgba(255,255,255,0.1)');
  ctx.fillStyle = gradient;
  ctx.fill();

  ctx.lineWidth = 4;
  ctx.strokeStyle = 'rgba(255,255,255,0.9)';
  ctx.stroke();

  return new THREE.CanvasTexture(canvas);
};

const drawRoundedRect = (ctx, x, y, width, height, radius) => {
  if (ctx.roundRect) {
    ctx.beginPath();
    ctx.roundRect(x, y, width, height, radius);
    return;
  }

  const r = Math.min(radius, width / 2, height / 2);
  ctx.beginPath();
  ctx.moveTo(x + r, y);
  ctx.lineTo(x + width - r, y);
  ctx.quadraticCurveTo(x + width, y, x + width, y + r);
  ctx.lineTo(x + width, y + height - r);
  ctx.quadraticCurveTo(x + width, y + height, x + width - r, y + height);
  ctx.lineTo(x + r, y + height);
  ctx.quadraticCurveTo(x, y + height, x, y + height - r);
  ctx.lineTo(x, y + r);
  ctx.quadraticCurveTo(x, y, x + r, y);
};

const createMapMarkerTexture = (type, color, code) => {
  const scale = 2;
  const canvas = document.createElement('canvas');
  canvas.width = 256 * scale;
  canvas.height = 256 * scale;
  const ctx = canvas.getContext('2d');

  ctx.shadowColor = 'rgba(0, 0, 0, 0.45)';
  ctx.shadowBlur = 16 * scale;
  ctx.shadowOffsetY = 4 * scale;

  if (type === 'person') {
    ctx.fillStyle = color;
    ctx.beginPath();
    ctx.arc(128 * scale, 78 * scale, 22 * scale, 0, Math.PI * 2);
    ctx.fill();
    drawRoundedRect(ctx, 92 * scale, 106 * scale, 72 * scale, 92 * scale, 26 * scale);
    ctx.fill();
  } else if (type === 'vehicle') {
    ctx.fillStyle = color;
    drawRoundedRect(ctx, 58 * scale, 104 * scale, 140 * scale, 60 * scale, 18 * scale);
    ctx.fill();
    ctx.fillRect(86 * scale, 80 * scale, 72 * scale, 34 * scale);
    ctx.clearRect(96 * scale, 88 * scale, 20 * scale, 16 * scale);
    ctx.clearRect(124 * scale, 88 * scale, 24 * scale, 16 * scale);
    ctx.fillStyle = '#12304f';
    ctx.beginPath();
    ctx.arc(92 * scale, 170 * scale, 18 * scale, 0, Math.PI * 2);
    ctx.arc(166 * scale, 170 * scale, 18 * scale, 0, Math.PI * 2);
    ctx.fill();
  } else if (type === 'equipment') {
    ctx.fillStyle = color;
    ctx.fillRect(74 * scale, 120 * scale, 108 * scale, 42 * scale);
    ctx.fillRect(100 * scale, 88 * scale, 44 * scale, 34 * scale);
    ctx.beginPath();
    ctx.moveTo(144 * scale, 102 * scale);
    ctx.lineTo(186 * scale, 72 * scale);
    ctx.lineWidth = 16 * scale;
    ctx.strokeStyle = color;
    ctx.stroke();
    ctx.beginPath();
    ctx.arc(82 * scale, 172 * scale, 16 * scale, 0, Math.PI * 2);
    ctx.arc(176 * scale, 172 * scale, 16 * scale, 0, Math.PI * 2);
    ctx.fill();
  } else {
    ctx.fillStyle = color;
    ctx.beginPath();
    ctx.arc(128 * scale, 116 * scale, 38 * scale, 0, Math.PI * 2);
    ctx.fill();
    ctx.fillStyle = '#0b2540';
    ctx.beginPath();
    ctx.arc(128 * scale, 116 * scale, 16 * scale, 0, Math.PI * 2);
    ctx.fill();
  }

  ctx.shadowColor = 'transparent';
  ctx.font = `bold ${16 * scale}px "Microsoft YaHei", sans-serif`;
  const textWidth = ctx.measureText(code).width;
  const labelWidth = textWidth + 24 * scale;
  const labelHeight = 26 * scale;
  const labelX = 128 * scale - labelWidth / 2;
  const labelY = 182 * scale - labelHeight / 2;

  ctx.fillStyle = 'rgba(0, 0, 0, 0.85)';
  drawRoundedRect(ctx, labelX, labelY, labelWidth, labelHeight, 4 * scale);
  ctx.fill();

  ctx.lineWidth = 1 * scale;
  ctx.strokeStyle = 'rgba(255, 255, 255, 0.4)';
  ctx.stroke();
  ctx.fillStyle = '#fff';
  ctx.fillText(code, 128 * scale, 182 * scale);

  const texture = new THREE.CanvasTexture(canvas);
  texture.minFilter = THREE.LinearFilter;
  texture.magFilter = THREE.LinearFilter;
  texture.generateMipmaps = false;
  return texture;
};

const createTextLabelTexture = (text, color) => {
  const canvas = document.createElement('canvas');
  canvas.width = 384;
  canvas.height = 96;
  const ctx = canvas.getContext('2d');

  ctx.font = 'bold 26px "Microsoft YaHei", sans-serif';
  const textWidth = ctx.measureText(text).width;
  const width = Math.min(344, textWidth + 36);
  const x = (canvas.width - width) / 2;
  const y = 18;

  ctx.fillStyle = 'rgba(7, 18, 34, 0.82)';
  drawRoundedRect(ctx, x, y, width, 50, 14);
  ctx.fill();

  ctx.strokeStyle = `${color}aa`;
  ctx.lineWidth = 2;
  ctx.stroke();

  ctx.fillStyle = color;
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  ctx.fillText(text, canvas.width / 2, y + 25);

  const texture = new THREE.CanvasTexture(canvas);
  texture.minFilter = THREE.LinearFilter;
  texture.magFilter = THREE.LinearFilter;
  texture.generateMipmaps = false;
  return texture;
};

const getTerrainHeight = (x, z) => Math.sin(x / 10) * Math.cos(z / 10) * 8;

const updateSelectionVisuals = () => {
  pointSprites.forEach((sprite, code) => {
    const isSelected = selectedUnitCode.value === code;
    sprite.userData.isSelected = isSelected;
    sprite.material.opacity = isSelected ? 1 : 0.92;
  });
};

const createIconTexture = (type, color, deviceId) => {
  const scale = 2;
  const canvas = document.createElement('canvas');
  canvas.width = 256 * scale;
  canvas.height = 256 * scale;
  const ctx = canvas.getContext('2d');

  const icons = { GNSS: '📍', DEEP: '⬍', RADAR: '📡', SURFACE: '🗺', CRACK: '🪨', FIRE: '🔥', WATER: '💧', GROUND: '🧭', STRESS: '📳', VIB: '📢', SAT: '🛰' };
  const emoji = icons[type] || '●';

  ctx.save();
  ctx.translate(128 * scale, 112 * scale);
  ctx.rotate(-45 * Math.PI / 180);
  ctx.beginPath();
  if (ctx.roundRect) {
    ctx.roundRect(-24 * scale, -24 * scale, 48 * scale, 48 * scale, [24 * scale, 24 * scale, 24 * scale, 0]);
  } else {
    ctx.rect(-24 * scale, -24 * scale, 48 * scale, 48 * scale);
  }
  ctx.fillStyle = '#fff';
  ctx.shadowColor = 'rgba(0,0,0,0.6)';
  ctx.shadowBlur = 12 * scale;
  ctx.shadowOffsetY = 4 * scale;
  ctx.fill();

  ctx.shadowColor = 'transparent';
  ctx.lineWidth = 3 * scale;
  ctx.strokeStyle = '#85C6F1';
  ctx.stroke();
  ctx.restore();

  ctx.font = `${28 * scale}px Arial`;
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  ctx.fillText(emoji, 128 * scale, 112 * scale);

  ctx.font = `bold ${16 * scale}px "Microsoft YaHei", sans-serif`;
  const textWidth = ctx.measureText(deviceId).width;
  const labelWidth = textWidth + 24 * scale;
  const labelHeight = 26 * scale;
  const labelX = 128 * scale - labelWidth / 2;
  const labelY = 182 * scale - labelHeight / 2;

  ctx.fillStyle = 'rgba(0, 0, 0, 0.85)';
  if (ctx.roundRect) {
    ctx.beginPath();
    ctx.roundRect(labelX, labelY, labelWidth, labelHeight, 4 * scale);
    ctx.fill();
  } else {
    ctx.fillRect(labelX, labelY, labelWidth, labelHeight);
  }

  ctx.lineWidth = 1 * scale;
  ctx.strokeStyle = 'rgba(255, 255, 255, 0.4)';
  ctx.stroke();

  ctx.fillStyle = '#fff';
  ctx.fillText(deviceId, 128 * scale, 182 * scale);

  const texture = new THREE.CanvasTexture(canvas);
  texture.minFilter = THREE.LinearFilter;
  texture.magFilter = THREE.LinearFilter;
  texture.generateMipmaps = false;
  return texture;
};

const createTerrainZone = (centerX, centerZ, colorHex, spotTexture) => {
  const geometry = new THREE.PlaneGeometry(60, 60, 64, 64);
  geometry.rotateX(-Math.PI / 2);

  const pos = geometry.attributes.position;
  for (let i = 0; i < pos.count; i++) {
    const vx = pos.getX(i) + centerX;
    const vz = pos.getZ(i) + centerZ;
    const vy = Math.sin(vx / 10) * Math.cos(vz / 10) * 8;
    pos.setY(i, vy - 0.5);
  }

  geometry.translate(centerX, 0, centerZ);
  geometry.computeVertexNormals();

  const material = new THREE.MeshBasicMaterial({
    color: colorHex,
    map: spotTexture,
    transparent: true,
    depthWrite: false,
    blending: THREE.NormalBlending,
    opacity: 0.8
  });

  const mesh = new THREE.Mesh(geometry, material);
  mesh.renderOrder = 1;
  return mesh;
};

const initMap = () => {
  if (!threeContainer.value) return;

  scene = new THREE.Scene();
  scene.background = new THREE.Color(0x0a1a2a);

  const width = threeContainer.value.clientWidth;
  const height = threeContainer.value.clientHeight;

  camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000);
  camera.position.set(12, 86, 26);

  renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.setSize(width, height);
  renderer.domElement.style.display = 'block';
  threeContainer.value.appendChild(renderer.domElement);

  renderer.domElement.addEventListener('contextmenu', (event) => event.preventDefault());

  controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;
  controls.dampingFactor = 0.08;
  controls.panSpeed = 1.2;
  controls.screenSpacePanning = false;
  controls.maxPolarAngle = Math.PI / 2 - 0.05;
  controls.mouseButtons = {
    LEFT: THREE.MOUSE.PAN,
    MIDDLE: THREE.MOUSE.DOLLY,
    RIGHT: null
  };
  controls.rotateKey = 'Control';
  controls.target.set(18, 0, 16);

  if (false) {
  const glowTexture = createGlowTexture();
  const spotTexture = createSpotTexture();
  const allPointsGroup = new THREE.Group();

  const icons = { GNSS: '📍', DEEP: '⬍', RADAR: '📡', SURFACE: '🗺', CRACK: '🪨', FIRE: '🔥', WATER: '💧', GROUND: '🧭', STRESS: '📳', VIB: '📢', SAT: '🛰' };
  const types = Object.keys(icons);
  const colors = ['#F57676', '#FFA500', '#fadb14', '#66B1FF', '#71C446'];
  const zoneCenters = [
    { x: 35, z: 35 },
    { x: -35, z: 35 },
    { x: -35, z: -35 },
    { x: 35, z: -35 }
  ];

  for (let i = 0; i < 4; i++) {
    const zoneMesh = createTerrainZone(zoneCenters[i].x, zoneCenters[i].z, colors[i], spotTexture);
    scene.add(zoneMesh);
  }

  let redGnssCount = 0;
  const positions = [];
  const colorArray = [];
  const baseColorObj = new THREE.Color();

  for (let i = 0; i < 150; i++) {
    let type = types[i % types.length];
    if (i < 7) type = 'RADAR';
    else if (type === 'RADAR') type = 'GNSS';

    let alarmIdx = (i * 7) % 5;
    const isOnline = i % 8 !== 0;
    if (type === 'GNSS' && alarmIdx === 0) {
      if (redGnssCount < 2) redGnssCount++;
      else alarmIdx = 4;
    }

    const targetColor = isOnline ? colors[alarmIdx] : '#999999';
    const deviceId = `${type}${i}`;

    let x;
    let z;
    if (isOnline && alarmIdx < 4) {
      const center = zoneCenters[alarmIdx];
      const radius = Math.random() * 20;
      const angle = Math.random() * Math.PI * 2;
      x = center.x + Math.cos(angle) * radius;
      z = center.z + Math.sin(angle) * radius;
    } else {
      let isValid = false;
      while (!isValid) {
        x = (Math.random() - 0.5) * 160;
        z = (Math.random() - 0.5) * 160;
        isValid = true;
        for (let j = 0; j < zoneCenters.length; j++) {
          const dist = Math.sqrt(Math.pow(x - zoneCenters[j].x, 2) + Math.pow(z - zoneCenters[j].z, 2));
          if (dist <= 25) {
            isValid = false;
            break;
          }
        }
      }
    }

    const y = Math.sin(x / 10) * Math.cos(z / 10) * 8;

    positions.push(x, y, z);
    baseColorObj.set(targetColor);
    colorArray.push(baseColorObj.r, baseColorObj.g, baseColorObj.b);

    const iconTexture = createIconTexture(type, targetColor, deviceId);
    const spriteMaterial = new THREE.SpriteMaterial({
      map: iconTexture,
      transparent: true,
      depthTest: true,
      depthWrite: false
    });

    const sprite = new THREE.Sprite(spriteMaterial);
    sprite.renderOrder = 10;
    sprite.position.set(x, y, z);
    sprite.scale.set(12, 12, 1);
    sprite.center.set(0.5, 1 - 160 / 256);

    if (alarmIdx === 0 && isOnline) {
      sprite.userData.isBlinking = true;
    }

    allPointsGroup.add(sprite);
  }

  const geometry = new THREE.BufferGeometry();
  geometry.setAttribute('position', new THREE.Float32BufferAttribute(positions, 3));
  geometry.setAttribute('color', new THREE.Float32BufferAttribute(colorArray, 3));

  const pointsMaterial = new THREE.PointsMaterial({
    size: 8,
    map: glowTexture,
    transparent: true,
    blending: THREE.AdditiveBlending,
    depthTest: true,
    depthWrite: false,
    vertexColors: true
  });

  const points = new THREE.Points(geometry, pointsMaterial);
  points.renderOrder = 2;

  allPointsGroup.add(points);
  scene.add(allPointsGroup);
  }

  const spotTexture = createSpotTexture();
  const allPointsGroup = new THREE.Group();
  const routeGroup = new THREE.Group();
  pointSprites = new Map();
  const warningZones = [{ x: 24, z: 24, color: '#ff8a4c' }];

  warningZones.forEach((zone) => {
    const zoneMesh = createTerrainZone(zone.x, zone.z, zone.color, spotTexture);
    zoneMesh.scale.set(1.2, 1, 1.05);
    scene.add(zoneMesh);
  });

  mapUnits.forEach((unit) => {
    const y = getTerrainHeight(unit.x, unit.z);
    const iconTexture = createMapMarkerTexture(unit.type, unit.color, unit.code);
    const spriteMaterial = new THREE.SpriteMaterial({
      map: iconTexture,
      transparent: true,
      depthTest: true,
      depthWrite: false
    });

    const sprite = new THREE.Sprite(spriteMaterial);
    sprite.renderOrder = 10;
    sprite.position.set(unit.x, y, unit.z);
    sprite.scale.set(12, 12, 1);
    sprite.center.set(0.5, 1 - 160 / 256);
    sprite.userData.isBlinking = Boolean(unit.blink);
    sprite.userData.baseScale = 12;
    sprite.userData.code = unit.code;
    allPointsGroup.add(sprite);
    pointSprites.set(unit.code, sprite);
  });

  scene.add(allPointsGroup);
  updateSelectionVisuals();

  mapRoutes.forEach((route) => {
    const curvePoints = route.points.map(([x, z]) => {
      const y = Math.sin(x / 10) * Math.cos(z / 10) * 8 + 1.8;
      return new THREE.Vector3(x, y, z);
    });

    const curve = new THREE.CatmullRomCurve3(curvePoints);
    const sampled = curve.getPoints(60);
    const geometry = new THREE.BufferGeometry().setFromPoints(sampled);
    const material = new THREE.LineBasicMaterial({
      color: route.color,
      transparent: true,
      opacity: 0.95
    });

    const line = new THREE.Line(geometry, material);
    routeGroup.add(line);

    const [labelX, labelZ] = route.labelAt;
    const labelY = Math.sin(labelX / 10) * Math.cos(labelZ / 10) * 8 + 5;
    const labelTexture = createTextLabelTexture(route.label, route.color);
    const labelMaterial = new THREE.SpriteMaterial({
      map: labelTexture,
      transparent: true,
      depthWrite: false
    });
    const labelSprite = new THREE.Sprite(labelMaterial);
    labelSprite.position.set(labelX, labelY, labelZ);
    labelSprite.scale.set(18, 4.5, 1);
    routeGroup.add(labelSprite);
  });

  scene.add(routeGroup);

  const gridHelper = new THREE.GridHelper(200, 30, 0x1c3d90, 0x1c3d90);
  gridHelper.position.y = -10;
  gridHelper.material.opacity = 0.15;
  gridHelper.material.transparent = true;
  scene.add(gridHelper);

  const animate = () => {
    animationId = requestAnimationFrame(animate);
    const time = Date.now() * 0.005;

    pointSprites.forEach((sprite, code) => {
      const isSelected = selectedUnitCode.value === code;
      const isBlinking = sprite.userData.isBlinking;
      const baseScale = sprite.userData.baseScale || 12;
      const pulse = isSelected ? 2.4 : isBlinking ? 1.2 : 0;
      const scale = baseScale + Math.sin(time * (isSelected ? 1.4 : 1)) * pulse;
      sprite.scale.set(scale, scale, 1);
    });

    controls.update();
    renderer.render(scene, camera);
  };

  animate();
};

const handleResize = () => {
  if (!threeContainer.value || !renderer || !camera) return;
  const width = threeContainer.value.clientWidth;
  const height = threeContainer.value.clientHeight;
  camera.aspect = width / height;
  camera.updateProjectionMatrix();
  renderer.setSize(width, height);
};

onMounted(() => {
  initMap();
  window.addEventListener('resize', handleResize);
});

onUnmounted(() => {
  cancelAnimationFrame(animationId);
  window.removeEventListener('resize', handleResize);
  if (controls) controls.dispose();
  if (renderer) {
    renderer.dispose();
    if (renderer.domElement && renderer.domElement.parentNode) {
      renderer.domElement.parentNode.removeChild(renderer.domElement);
    }
  }
});
</script>

<style scoped>
.emergency-page {
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
    linear-gradient(135deg, rgba(28, 61, 144, 0.08), rgba(89, 170, 255, 0.03)),
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
  max-width: 700px;
  line-height: 1.7;
  color: #5d7192;
}

.hero-actions {
  display: flex;
  align-items: flex-start;
  gap: 12px;
}

.page-hero .hero-actions {
  display: none;
}

.primary-btn,
.ghost-btn {
  border-radius: 999px;
  padding: 10px 18px;
  font-size: 13px;
  cursor: pointer;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.primary-btn {
  border: none;
  color: #fff;
  background: linear-gradient(135deg, #1c64f2, #163c8f);
  box-shadow: 0 12px 24px rgba(28, 100, 242, 0.24);
}

.ghost-btn {
  border: 1px solid rgba(28, 61, 144, 0.16);
  color: #1c3d90;
  background: #f8fbff;
}

.primary-btn:hover,
.ghost-btn:hover {
  transform: translateY(-1px);
}

.ghost-btn.small {
  padding: 7px 12px;
  font-size: 12px;
}

.panel {
  padding: 20px;
}

.command-board {
  padding: 14px;
}

.panel-head {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 12px;
  margin-bottom: 18px;
}

.featured-plan-head {
  position: relative;
  min-height: 96px;
  padding-right: 440px;
}

.featured-plan-head > .badge.warning {
  display: none;
}

.panel-head-actions {
  position: absolute;
  top: 0;
  right: 0;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 14px;
}

.command-board .panel-head {
  margin-bottom: 10px;
}

.panel-head h3 {
  margin-top: 4px;
  font-size: 20px;
  color: #17376f;
}

.command-board .panel-head h3 {
  font-size: 16px;
}

.command-board .panel-kicker {
  font-size: 11px;
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

.command-board .badge {
  min-width: 72px;
  padding: 5px 10px;
  font-size: 11px;
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

.command-layout {
  display: grid;
  grid-template-columns: minmax(0, 1.18fr) minmax(400px, 0.92fr);
  gap: 16px;
  align-items: stretch;
}

.map-board {
  position: relative;
  min-height: 500px;
  overflow: hidden;
  border-radius: 18px;
  border: 1px solid rgba(133, 198, 241, 0.18);
  background: #0a1a2a;
}

.three-viewport {
  position: relative;
  width: 100%;
  height: 500px;
  cursor: grab;
  background: #0a1a2a;
}

.three-viewport:active {
  cursor: grabbing;
}

.map-overlay {
  position: absolute;
  z-index: 3;
  pointer-events: none;
  border-radius: 999px;
  font-size: 11px;
  line-height: 1;
}

.warning-chip {
  top: 12px;
  left: 12px;
  padding: 12px 20px;
  color: #fff0df;
  background: rgba(255, 138, 76, 0.2);
  border: 1px solid rgba(255, 138, 76, 0.38);
  box-shadow: 0 12px 28px rgba(255, 138, 76, 0.24);
  font-size: 16px;
  font-weight: 700;
}

.controls-chip {
  top: 14px;
  right: 14px;
  padding: 7px 11px;
  color: #d8efff;
  background: rgba(7, 18, 34, 0.72);
  border: 1px solid rgba(133, 198, 241, 0.18);
}

.board-sidebar {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.sidebar-summary {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

.summary-card,
.plan-meta div,
.resource-card,
.snapshot-card {
  padding: 12px 14px;
  border-radius: 16px;
  background: #f7faff;
  border: 1px solid rgba(58, 103, 187, 0.08);
}

.summary-card span,
.plan-meta span,
.resource-card span,
.snapshot-card span,
.unit-cell p {
  font-size: 12px;
  color: #7890b1;
}

.summary-card strong,
.plan-meta strong,
.resource-card strong,
.unit-cell strong {
  display: block;
  margin-top: 6px;
  font-size: 14px;
}

.board-list {
  display: grid;
  gap: 8px;
  max-height: 404px;
  overflow-y: auto;
  padding-right: 4px;
}

.board-list::-webkit-scrollbar {
  width: 8px;
}

.board-list::-webkit-scrollbar-thumb {
  border-radius: 999px;
  background: rgba(92, 128, 190, 0.35);
}

.board-list::-webkit-scrollbar-track {
  background: rgba(237, 244, 255, 0.7);
}

.board-table {
  display: grid;
  grid-template-columns: 0.82fr 1.18fr 1.1fr 0.78fr;
  gap: 8px;
  align-items: center;
  padding: 10px 12px;
  border-radius: 14px;
}

.table-head {
  background: #edf4ff;
  color: #5f7eb2;
  font-size: 11px;
  font-weight: 700;
}

.table-row {
  background: #f8fbff;
  border: 1px solid rgba(28, 61, 144, 0.06);
  cursor: pointer;
  transition: border-color 0.2s ease, box-shadow 0.2s ease, transform 0.2s ease;
}

.table-row:hover,
.table-row.selected {
  border-color: rgba(28, 100, 242, 0.28);
  box-shadow: 0 10px 24px rgba(28, 100, 242, 0.12);
  transform: translateY(-1px);
}

.unit-cell p {
  margin-top: 4px;
  font-size: 11px;
}

.overview-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 18px;
}

.featured-plan-layout {
  display: grid;
  grid-template-columns: 1.2fr 0.8fr;
  gap: 18px;
  align-items: start;
}

.detail-grid {
  display: grid;
  grid-template-columns: 1.1fr 0.9fr 0.95fr;
  gap: 18px;
}

.plan-meta {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
  margin-bottom: 18px;
}

.route-summary {
  padding: 16px;
  border-radius: 18px;
  background: linear-gradient(180deg, #f8fbff, #f4f8ff);
  border: 1px solid rgba(28, 61, 144, 0.08);
}

.route-summary-head h4 {
  margin-top: 4px;
  font-size: 18px;
  color: #17376f;
}

.route-summary-list {
  display: grid;
  gap: 12px;
  margin-top: 16px;
}

.route-summary-item {
  padding: 14px 14px 14px 16px;
  border-radius: 14px;
  background: #fff;
  border-left: 4px solid #85c6f1;
}

.route-summary-item strong {
  display: block;
  color: #1c3d90;
  font-size: 14px;
}

.route-summary-item p {
  margin-top: 6px;
  line-height: 1.7;
  color: #5f728f;
  font-size: 13px;
}

.action-flow,
.timeline,
.resource-cards,
.feedback-list {
  display: grid;
  gap: 12px;
}

.flow-item {
  display: grid;
  grid-template-columns: 48px 1fr auto;
  gap: 14px;
  align-items: center;
  padding: 14px 16px;
  border-radius: 16px;
  border: 1px solid rgba(28, 61, 144, 0.08);
}

.flow-item.done { background: #f1faf5; }
.flow-item.active { background: #fff7eb; }
.flow-item.pending { background: #f7f9fd; }

.flow-index {
  width: 48px;
  height: 48px;
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #eaf2ff;
  color: #1c3d90;
  font-weight: 700;
}

.flow-content p,
.timeline-body p,
.feedback-item p,
.resource-card p {
  margin-top: 6px;
  line-height: 1.6;
  color: #5f728f;
  font-size: 13px;
}

.flow-state {
  font-size: 12px;
  color: #7388a7;
}

.timeline-item {
  display: grid;
  grid-template-columns: 20px 1fr;
  gap: 12px;
}

.timeline-track {
  position: relative;
  width: 20px;
}

.timeline-track::before {
  content: '';
  position: absolute;
  left: 8px;
  top: 4px;
  bottom: -12px;
  width: 2px;
  background: #dfe8f6;
}

.timeline-track::after {
  content: '';
  position: absolute;
  top: 6px;
  left: 3px;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: #b9c9e4;
}

.timeline-item.done .timeline-track::after { background: #1d8e5a; }
.timeline-item.active .timeline-track::after { background: #ef9a35; }

.timeline-body {
  padding: 14px 16px;
  border-radius: 16px;
  background: #f8fbff;
}

.timeline-top {
  display: flex;
  justify-content: space-between;
  gap: 12px;
  font-size: 13px;
}

.timeline-top span {
  color: #7086a7;
}

.feedback-item {
  display: flex;
  justify-content: space-between;
  gap: 12px;
  padding: 14px 16px;
  border-radius: 16px;
  background: #f8fbff;
}

.feedback-extra {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  margin-top: 12px;
}

.snapshot-placeholder {
  margin-top: 10px;
  min-height: 110px;
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #7d94b7;
  background: linear-gradient(135deg, #eef5ff, #dfeafb);
  border: 1px dashed rgba(28, 61, 144, 0.18);
}

.archive-lines {
  margin-top: 10px;
  display: grid;
  gap: 10px;
  color: #5f728f;
  font-size: 13px;
}

@media (max-width: 1200px) {
  .command-layout,
  .detail-grid,
  .featured-plan-layout {
    grid-template-columns: 1fr;
  }

  .sidebar-summary {
    grid-template-columns: 1fr;
  }

  .featured-plan-head {
    padding-right: 0;
    min-height: 0;
  }

  .panel-head-actions {
    position: static;
    width: 100%;
    justify-content: flex-start;
    flex-wrap: wrap;
  }
}

@media (max-width: 768px) {
  .page-hero {
    flex-direction: column;
  }

  .plan-meta,
  .feedback-extra,
  .board-table {
    grid-template-columns: 1fr;
  }

  .page-title {
    font-size: 26px;
  }

  .map-board {
    min-height: 420px;
  }

  .three-viewport {
    height: 420px;
  }
}
</style>
