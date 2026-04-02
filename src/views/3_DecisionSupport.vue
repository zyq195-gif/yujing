<template>
  <div class="view-page emergency-page">
    <section class="module-section">
      <div class="section-header">
        <h3 class="section-title">智能决策支持与专家知识库</h3>
        <div class="top-tabs">
          <div class="tab-item" :class="{ active: activeTab === 'decision' }" @click="activeTab = 'decision'">
            <span class="icon"></span> 智能决策支持
          </div>
          <div class="tab-item" :class="{ active: activeTab === 'expert' }" @click="activeTab = 'expert'">
            <span class="icon"></span> 专家知识库与案例推理
          </div>
        </div>
      </div>

      <div v-show="activeTab === 'decision'" class="tab-content">
        <div class="layout-grid">
          <div class="left-panel">
            <div class="sys-card warning-status-card">
              <div class="card-header">
                <div class="header-icon-bg"><span class="card-icon">📡</span></div>
                <span class="card-title-text">当前系统预警感知</span>
              </div>

              <div class="alert-box level-1 breathing-alert">
                <div class="alert-title">
                  <span class="alert-dot pulse-dot"></span>
                  <strong>一级预警 (红色) 触发</strong>
                </div>
                <div class="alert-details">
                  <div class="detail-row">
                    <span class="detail-label">监测位置：</span>
                    <span class="detail-value">南帮 2区 GN8855, Z位移突变</span>
                  </div>
                  <div class="detail-row">
                    <span class="detail-label">当前速率：</span>
                    <span class="detail-value danger-text">210 mm/d (远超阈值)</span>
                  </div>
                  <div class="detail-row">
                    <span class="detail-label">外部条件：</span>
                    <span class="detail-value text-wrap">近期连续强降雨，区域存在已知软弱夹层</span>
                  </div>
                </div>
              </div>

              <div class="analysis-action">
                <p class="action-desc">系统已捕获高危险情特征，是否立即驱动规则引擎与案例库进行综合推理？</p>
                <button class="primary-btn pulse-btn large-btn" @click="generateReport" :disabled="isGenerating">
                  <span v-if="!isGenerating && !reportGenerated">⚡ 启动多维智能推理</span>
                  <span v-else-if="isGenerating">⏳ 规则引擎高速计算中...</span>
                  <span v-else>🔄 重新分析研判</span>
                </button>
              </div>
            </div>

            <div class="sys-card judgment-card" :class="{ 'show': reportGenerated }">
              <div class="card-header">
                <div class="header-icon-bg purple-bg"><span class="card-icon">⚙️</span></div>
                <span class="card-title-text">规则驱动研判结论</span>
              </div>
              <div class="judgment-content" v-if="reportGenerated">
                <div class="judge-item">
                  <span class="label">力学机制研判</span>
                  <span class="value highlight-text">典型受弱层面控制的切层-顺层滑动风险</span>
                </div>
                <div class="judge-item">
                  <span class="label">主导诱因匹配</span>
                  <span class="value">降雨入渗导致孔隙水压力骤升，软弱结构面抗剪强度锐减</span>
                </div>
                <div class="judge-item">
                  <span class="label">推荐处治方向</span>
                  <span class="value action-text">阻水排水 + 坡脚反压 + 深部结构面增强</span>
                </div>
              </div>
              <div class="empty-text" v-else>
                <span class="empty-placeholder-icon">☕</span>
                <p>等待启动推理...</p>
              </div>
            </div>
          </div>

          <div class="sys-card report-card">
            <div class="card-header report-header sticky-header">
              <div class="header-icon-bg blue-bg"><span class="card-icon">📄</span></div>
              <span class="card-title-text">《边坡险情处治初步建议》</span>
              <span class="report-time" v-if="reportGenerated">生成时间：{{ currentTime }}</span>
            </div>

            <div class="scrollable-report-body">
              <div v-if="!reportGenerated && !isGenerating" class="empty-state">
                <div class="empty-img">🔍</div>
                <p>综合案例类比与规则推理，自动生成结构化处治建议</p>
              </div>

              <div v-if="isGenerating" class="loading-state">
                <div class="spinner"></div>
                <p>正在计算多维相似度，检索历史案例...</p>
                <div class="loading-bar"><div class="progress"></div></div>
              </div>

              <div v-if="reportGenerated" class="generated-report slide-in">
                <div class="report-section">
                  <div class="section-tag"><span class="tag-dot"></span> 案例类比推理 (最相似历史案例)</div>
                  <div class="analogy-list">
                    <div class="analogy-item">
                      <div class="analogy-info">
                        <span class="case-name">🔗 [南帮-2024] 牵引式滑坡局部支挡与压帮协同治理案例</span>
                        <span class="case-effect success">治理成功</span>
                      </div>
                      <div class="match-bar-container">
                        <span class="match-label">多维相似度匹配：</span>
                        <div class="match-track"><div class="match-fill" style="width: 92%;"></div></div>
                        <span class="match-percent">92%</span>
                      </div>
                    </div>
                    <div class="analogy-item">
                      <div class="analogy-info">
                        <span class="case-name">🔗 [东区-2023] 强降雨诱发软弱结构面滑动换填治理案例</span>
                        <span class="case-effect success">治理成功</span>
                      </div>
                      <div class="match-bar-container">
                        <span class="match-label">多维相似度匹配：</span>
                        <div class="match-track"><div class="match-fill" style="width: 85%;"></div></div>
                        <span class="match-percent">85%</span>
                      </div>
                    </div>
                  </div>
                </div>

                <div class="report-section mt-15">
                  <div class="section-tag"><span class="tag-dot"></span> 综合治理方案建议</div>

                  <div class="section-content-grid">
                    <div class="advice-card neutral-card">
                      <h4><span class="block-icon">📌</span> 处置原则</h4>
                      <p>坚持“安全第一、先控后治”。当前位移速率急剧上升，首要任务是撤离危险区人员和设备。在控制变形发展的前提下，结合降雨情况进行地表截排水，稳固坡脚。</p>
                    </div>

                    <div class="advice-card warning-card">
                      <h4><span class="block-icon">🛠️</span> 推荐工法</h4>
                      <div class="plan-item">
                        <span class="plan-badge primary-badge">主方案</span>
                        <span class="plan-desc"><b>局部压帮</b> —— 在坡脚区域就近取料进行反压，迅速提升抗滑力，抑制前缘剪出。</span>
                      </div>
                      <div class="plan-divider"></div>
                      <div class="plan-item">
                        <span class="plan-badge secondary-badge">辅助方案</span>
                        <span class="plan-desc"><b>换填软弱结构面 + 仰斜排水孔</b> —— 结合局部截排水，降低孔隙水压力，阻断降雨进一步入渗。</span>
                      </div>
                    </div>

                    <div class="advice-card info-card">
                      <h4><span class="block-icon">🔑</span> 施工技术要点</h4>
                      <ul class="styled-points-list">
                        <li>压帮物料需就近取用，压帮高度及宽度需根据滑体推力精确计算。</li>
                        <li>施工期间必须保持 24小时 不间断雷达与GNSS协同立体监测，设定二级撤离预警。</li>
                        <li>坡顶后缘裂缝需立即使用粘土填缝封闭并夯实，铺设防渗膜。</li>
                      </ul>
                    </div>
                  </div>
                </div>

                <div class="report-actions">
                  <button class="secondary-btn" @click="handleExportPDF">导出 PDF 报告</button>
                  <button class="primary-btn" @click="handleIssueCommand">下发应急指挥及协同部门</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-show="activeTab === 'expert'" class="tab-content expert-library">
        <div class="sys-card full-height-card">
          <div class="sub-tab-container">
            <div class="sub-tabs">
              <span class="sub-tab" :class="{ active: expertSubTab === 'cases' }" @click="expertSubTab = 'cases'">治理方案案例库</span>
              <span class="sub-tab" :class="{ active: expertSubTab === 'rules' }" @click="expertSubTab = 'rules'">处治经验规则库</span>
            </div>

            <div class="toolbar-actions" v-if="expertSubTab === 'cases'">
              <div class="search-box">
                <input type="text" placeholder="多维检索 (如：牵引式、泥岩、压帮)..." class="ctrl-input search-input">
                <button class="secondary-btn icon-btn">🔍</button>
              </div>
              <button class="primary-btn add-btn" @click="openAddCaseModal">+ 结构化录入新案例</button>
            </div>

            <div class="toolbar-actions" v-if="expertSubTab === 'rules'">
              <div class="search-box">
                <input type="text" placeholder="检索规则编号或特征..." class="ctrl-input search-input">
                <button class="secondary-btn icon-btn">🔍</button>
              </div>
              <button class="primary-btn add-btn" @click="handleAddRule">+ 新增经验规则</button>
            </div>
          </div>

          <div class="table-container" v-show="expertSubTab === 'cases'">
            <table class="custom-table">
              <thead>
                <tr>
                  <th width="12%">案例编号</th>
                  <th width="28%">多维标签体系 (特征画像)</th>
                  <th width="20%">地质条件与诱因</th>
                  <th width="20%">采用治理方案</th>
                  <th width="10%">效果评估</th>
                  <th width="10%">操作</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, index) in historicalCases" :key="index">
                  <td class="case-id">{{ item.id }}</td>
                  <td>
                    <div class="tags-container">
                      <span class="dim-tag type" v-if="item.dim.type">{{ item.dim.type }}</span>
                      <span class="dim-tag rock" v-if="item.dim.rock">{{ item.dim.rock }}</span>
                      <span class="dim-tag trigger" v-if="item.dim.trigger">{{ item.dim.trigger }}</span>
                      <span class="dim-tag method" v-if="item.dim.method">{{ item.dim.method }}</span>
                    </div>
                  </td>
                  <td><div class="truncate-text" :title="item.condition">{{ item.condition }}</div></td>
                  <td><div class="truncate-text" :title="item.plan">{{ item.plan }}</div></td>
                  <td><span class="status-tag success">成功</span></td>
                  <td>
                    <div class="action-buttons">
                      <button class="text-btn" @click="handleViewCase(index)">查阅详情</button>
                      <button class="text-btn danger-text" @click="handleDeleteCase(index)">删除</button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>

          <div class="table-container" v-show="expertSubTab === 'rules'">
            <table class="custom-table">
              <thead>
                <tr>
                  <th width="12%">规则编号</th>
                  <th width="25%">感知与临界条件 (If)</th>
                  <th width="28%">推荐研判与处治动作 (Then)</th>
                  <th width="15%">经验参数</th>
                  <th width="8%">状态</th>
                  <th width="12%">操作</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(rule, index) in ruleBase" :key="index">
                  <td class="rule-id">{{ rule.id }}</td>
                  <td class="rule-condition">{{ rule.condition }}</td>
                  <td>{{ rule.action }}</td>
                  <td class="rule-param">{{ rule.parameter }}</td>
                  <td>
                    <span class="status-tag" :class="rule.isActive ? 'active' : 'inactive'">
                      {{ rule.isActive ? '启用' : '禁用' }}
                    </span>
                  </td>
                  <td>
                    <div class="action-buttons">
                      <button class="text-btn" @click="toggleRuleStatus(index)">{{ rule.isActive ? '禁用' : '启用' }}</button>
                      <button class="text-btn" @click="handleEditRule(index)">编辑</button>
                      <button class="text-btn danger-text" @click="handleDeleteRule(index)">删除</button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </section>

    <div class="modal-overlay" v-if="showModal">
      <div class="modal-box">
        <div class="modal-header">
          <h3>{{ isViewOnly ? '查看历史治理案例详情' : '录入成功治理案例 (标准化模板)' }}</h3>
          <span class="close-btn" @click="showModal = false">✖</span>
        </div>
        <div class="modal-body scrollable-modal">
          <div class="form-section-title">基础与多维标签</div>
          <div class="form-grid">
            <div class="form-item full-width">
              <label>案例名称/编号 <span class="req" v-if="!isViewOnly">*</span></label>
              <input type="text" class="ctrl-input" :disabled="isViewOnly" placeholder="例如：2025-01 南帮局部滑坡治理">
            </div>
            <div class="form-item">
              <label>灾害类型</label>
              <select class="ctrl-input" :disabled="isViewOnly"><option>牵引式滑坡</option><option>推移式滑坡</option><option>浅层溜坍</option></select>
            </div>
            <div class="form-item">
              <label>地质岩性</label>
              <select class="ctrl-input" :disabled="isViewOnly"><option>含泥岩软弱夹层</option><option>风化砂岩</option><option>断层破碎带</option></select>
            </div>
            <div class="form-item">
              <label>主导诱因</label>
              <select class="ctrl-input" :disabled="isViewOnly"><option>强降雨入渗</option><option>爆破震动</option><option>坡脚开挖过度</option></select>
            </div>
            <div class="form-item">
              <label>核心处治工法</label>
              <input type="text" class="ctrl-input" :disabled="isViewOnly" placeholder="如：局部支挡、压帮、换填">
            </div>
          </div>

          <div class="form-section-title mt-15">预警与地质详情</div>
          <div class="form-grid">
            <div class="form-item full-width">
              <label>触发预警阈值与监测特征</label>
              <input type="text" class="ctrl-input" :disabled="isViewOnly" placeholder="如：Z位移突变 > 150mm/d，深部位移呈三阶段蠕变">
            </div>
            <div class="form-item full-width">
              <label>详细地质条件说明</label>
              <textarea class="ctrl-input" rows="2" :disabled="isViewOnly" placeholder="描述具体地层倾向、倾角及地下水情况..."></textarea>
            </div>
          </div>

          <div class="form-section-title mt-15">方案与效果评估</div>
          <div class="form-grid">
            <div class="form-item full-width">
              <label>采用的治理方案 (主方案与辅助方案) <span class="req" v-if="!isViewOnly">*</span></label>
              <textarea class="ctrl-input" rows="2" :disabled="isViewOnly" placeholder="详细描述处治工法与设计参数..."></textarea>
            </div>
            <div class="form-item full-width">
              <label>施工技术要点</label>
              <textarea class="ctrl-input" rows="2" :disabled="isViewOnly" placeholder="施工过程中的关键控制点、安全要求..."></textarea>
            </div>
            <div class="form-item full-width">
              <label>最终效果评估</label>
              <input type="text" class="ctrl-input" :disabled="isViewOnly" placeholder="如：实施后位移收敛，安全系数提升至1.25，边坡稳定。">
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button class="secondary-btn" @click="showModal = false">{{ isViewOnly ? '关闭' : '取消' }}</button>
          <button class="primary-btn" v-if="!isViewOnly" @click="submitCase">保存并接入推理库</button>
        </div>
      </div>
    </div>

    <div class="modal-overlay" v-if="showRuleModal">
      <div class="modal-box rule-modal-box">
        <div class="modal-header">
          <h3>{{ editingRuleIndex === -1 ? '新增经验规则' : '编辑经验规则' }}</h3>
          <span class="close-btn" @click="showRuleModal = false">✖</span>
        </div>
        <div class="modal-body scrollable-modal">
          <div class="form-section-title">规则详情设定</div>
          <div class="form-grid single-col">
            <div class="form-item">
              <label>规则编号 <span class="req">*</span></label>
              <input type="text" class="ctrl-input" v-model="ruleForm.id" placeholder="如：RULE-R04-NEW">
            </div>
            <div class="form-item">
              <label>感知与临界条件 (If) <span class="req">*</span></label>
              <textarea class="ctrl-input" rows="3" v-model="ruleForm.condition" placeholder="描述触发该规则的前提条件..."></textarea>
            </div>
            <div class="form-item">
              <label>推荐研判与处治动作 (Then) <span class="req">*</span></label>
              <textarea class="ctrl-input" rows="3" v-model="ruleForm.action" placeholder="描述初步研判结论和推荐处治方案..."></textarea>
            </div>
            <div class="form-item">
              <label>经验参数 (提升范围/标准)</label>
              <input type="text" class="ctrl-input" v-model="ruleForm.parameter" placeholder="如：预估提升 Fs 0.05~0.15">
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button class="secondary-btn" @click="showRuleModal = false">取消</button>
          <button class="primary-btn" @click="submitRule">保存规则</button>
        </div>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, onMounted, reactive } from 'vue';

// Tab 切换状态
const activeTab = ref('decision'); // 'decision' 智能决策 或 'expert' 专家库
const expertSubTab = ref('cases'); // 'cases' 案例库 或 'rules' 规则库

// 智能决策报告生成状态
const isGenerating = ref(false);
const reportGenerated = ref(false);
const currentTime = ref('');

// 案例录入弹窗状态
const showModal = ref(false);
const isViewOnly = ref(false);

// 模拟历史案例数据
const historicalCases = ref([
  {
    id: 'CASE-2024-081',
    dim: { type: '牵引式滑坡', rock: '泥岩软弱夹层', trigger: '强降雨', method: '抗滑桩+压帮' },
    condition: '连续强降雨，顺层边坡，层间结合力弱，地下水丰富',
    plan: '主方案：抗滑桩；辅方案：坡脚大体积反压、地表截排水',
  },
  {
    id: 'CASE-2023-052',
    dim: { type: '软面滑动', rock: '断层破碎带', trigger: '地下水', method: '盲沟+换填' },
    condition: '地下水位持续偏高，断层破碎带裸露，土体软化软流',
    plan: '局部压帮 + 深部盲沟排水 + 换填软弱面',
  },
  {
    id: 'CASE-2023-115',
    dim: { type: '浅层溜坍', rock: '强风化砂岩', trigger: '坡度过陡', method: '削坡减载' },
    condition: '开挖边坡角过陡，表层岩体风化严重，遇水易崩解',
    plan: '顶部按1:1.5削坡减载 + 骨架植被护坡',
  },
  {
    id: 'CASE-2022-044',
    dim: { type: '深部滑动', rock: '层状节理岩体', trigger: '爆破震动', method: '锚索框架' },
    condition: '临近爆破作业区震动诱发，深层GNSS监测位移急剧增加',
    plan: '坡面预应力锚索框架梁加固，约束深部滑面',
  }
]);

// 模拟处治规则库数据
const ruleBase = ref([
  {
    id: 'RULE-R01-WATER',
    condition: 'IF 诱因为[强降雨/地下水] AND 岩性包含[软弱夹层]',
    action: 'THEN 初步研判为受水弱化控制滑动；必选方案需包含[截排水/阻水]',
    parameter: '抗剪强度 C/φ 折减预估: 15%~30%',
    isActive: true,
  },
  {
    id: 'RULE-R02-TOE',
    condition: 'IF 险情特征为[前缘剪出/坡脚隆起] AND 位移速率突增',
    action: 'THEN 初步研判为推移式破坏先兆；紧急推荐工法[坡脚局部压帮]',
    parameter: '压帮可提供阻滑力，预估提升 Fs 0.05~0.15',
    isActive: true,
  },
  {
    id: 'RULE-R03-DEEP',
    condition: 'IF 监测到[深层滑动面滑动] AND 深度 > 15m',
    action: 'THEN 常规挡墙无效；推荐选用[大截面抗滑桩/预应力锚索]',
    parameter: '单孔锚索设计拉力建议范围: 800~1500 kN',
    isActive: true,
  }
]);

// ================= 治理方案案例库操作 =================
const handleViewCase = (index) => {
  isViewOnly.value = true;
  showModal.value = true;
};

const handleDeleteCase = (index) => {
  if (window.confirm('确认要从专家库中永久删除该历史案例吗？')) {
    historicalCases.value.splice(index, 1);
  }
};

const openAddCaseModal = () => {
  isViewOnly.value = false;
  showModal.value = true;
};

const submitCase = () => {
  showModal.value = false;
};

// ================= 规则库管理操作 =================
const showRuleModal = ref(false);
const editingRuleIndex = ref(-1);
const ruleForm = reactive({
  id: '',
  condition: '',
  action: '',
  parameter: '',
  isActive: true
});

const handleAddRule = () => {
  editingRuleIndex.value = -1;
  ruleForm.id = `RULE-NEW-${Math.floor(Math.random() * 10000)}`;
  ruleForm.condition = '';
  ruleForm.action = '';
  ruleForm.parameter = '';
  ruleForm.isActive = true;
  showRuleModal.value = true;
};

const toggleRuleStatus = (index) => {
  ruleBase.value[index].isActive = !ruleBase.value[index].isActive;
};

const handleEditRule = (index) => {
  editingRuleIndex.value = index;
  const rule = ruleBase.value[index];
  ruleForm.id = rule.id;
  ruleForm.condition = rule.condition;
  ruleForm.action = rule.action;
  ruleForm.parameter = rule.parameter;
  ruleForm.isActive = rule.isActive;
  showRuleModal.value = true;
};

const handleDeleteRule = (index) => {
  if (window.confirm('确认删除该规则吗？')) {
    ruleBase.value.splice(index, 1);
  }
};

const submitRule = () => {
  if (editingRuleIndex.value === -1) {
    ruleBase.value.unshift({ ...ruleForm });
  } else {
    ruleBase.value[editingRuleIndex.value] = { ...ruleForm };
  }
  showRuleModal.value = false;
};

// ================= 模拟生成报告过程 =================
const generateReport = () => {
  isGenerating.value = true;
  reportGenerated.value = false;

  const now = new Date();
  currentTime.value = `${now.getFullYear()}/${String(now.getMonth()+1).padStart(2,'0')}/${String(now.getDate()).padStart(2,'0')} ${String(now.getHours()).padStart(2,'0')}:${String(now.getMinutes()).padStart(2,'0')}`;

  setTimeout(() => {
    isGenerating.value = false;
    reportGenerated.value = true;
  }, 1500); // 略微缩短加载时间，体验更流畅
};

// ================= 按钮弹窗提示事件 =================
const handleExportPDF = () => {
  window.alert('PDF 报告已成功导出！');
};

const handleIssueCommand = () => {
  window.alert('处治建议指令已成功下发至应急指挥及协同部门！');
};

onMounted(() => {
  generateReport();
});
</script>

<style scoped>
/* 全局页面布局 - 隐藏外层滚动，内部模块独立滚动 */
.view-page {
  display: flex;
  flex-direction: column;
  gap: 16px;
  height: 100%;
  padding: 16px;
  box-sizing: border-box;
  background: #f0f4f9;
  color: #16325c;
  overflow: hidden;
}

.module-section {
  display: flex;
  flex-direction: column;
  flex: 1;
  min-height: 0;
}

/* 顶部标题区 */
.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
  flex-shrink: 0;
}

.section-title {
  margin-top: 8px;
  font-size: 18px;
  color: #1c3d90;
  display: flex;
  align-items: center;
  border-left: 4px solid #1c64f2;
  border-radius: 2px;
  padding-left: 12px;
  margin-bottom: 0;
  font-weight: 700;
}

/* 胶囊式顶部选项卡 */
.top-tabs {
  display: flex;
  background: rgba(28, 61, 144, 0.05);
  border-radius: 14px;
  padding: 4px;
}

.tab-item {
  padding: 8px 24px;
  font-size: 14px;
  font-weight: bold;
  color: #5d7192;
  cursor: pointer;
  border-radius: 10px;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  align-items: center;
  gap: 6px;
}

.tab-item:hover { color: #1c64f2; }
.tab-item.active {
  background: #fff;
  color: #1c64f2;
  box-shadow: 0 4px 12px rgba(28, 61, 144, 0.08);
}

.tab-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-height: 0;
}

/* 左右分栏网格 */
.layout-grid {
  display: grid;
  grid-template-columns: 380px 1fr;
  gap: 24px;
  flex: 1;
  min-height: 0;
}

.left-panel {
  display: flex;
  flex-direction: column;
  gap: 20px;
  min-height: 0;
}

/* 统一圆钝高质感卡片 */
.sys-card {
  background: rgba(255, 255, 255, 0.94);
  border-radius: 20px;
  box-shadow: 0 12px 32px rgba(28, 61, 144, 0.05);
  border: 1px solid rgba(28, 61, 144, 0.08);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  transition: box-shadow 0.3s ease, transform 0.3s ease;
}

.full-height-card {
  flex: 1;
  min-height: 0;
  padding: 20px;
}

/* 独立浮动的卡片头 */
.card-header {
  display: flex;
  align-items: center;
  padding: 16px 20px;
  background: linear-gradient(180deg, #f8fbff, #fff);
  border-bottom: 1px solid rgba(28, 61, 144, 0.04);
  position: relative;
  z-index: 10;
}

.header-icon-bg {
  width: 32px; height: 32px;
  border-radius: 10px;
  background: #eef5fe;
  display: flex; align-items: center; justify-content: center;
  margin-right: 12px;
}
.header-icon-bg.purple-bg { background: #f3f0ff; }
.header-icon-bg.blue-bg { background: #e6f7ff; }

.card-icon { font-size: 16px; }
.card-title-text { font-size: 15px; font-weight: bold; color: #17376f; flex: 1; letter-spacing: 0.5px;}

/* ==== 模块一：左侧卡片区 ==== */
.alert-box {
  background: linear-gradient(135deg, #fffafa, #fff);
  border: 1px solid rgba(229, 57, 53, 0.12);
  border-radius: 16px;
  padding: 20px;
  margin: 10px 20px 20px 20px;
  box-shadow: 0 8px 24px rgba(229,57,53,0.06);
}
.alert-box.level-1 { border-left: 4px solid #e53935; }

.alert-title { display: flex; align-items: center; margin-bottom: 16px; }
.alert-dot { width: 8px; height: 8px; border-radius: 50%; background: #e53935; margin-right: 8px; }
@keyframes dotPulse {
  0% { box-shadow: 0 0 0 0 rgba(229, 57, 53, 0.4); }
  70% { box-shadow: 0 0 0 8px rgba(229, 57, 53, 0); }
  100% { box-shadow: 0 0 0 0 rgba(229, 57, 53, 0); }
}
.pulse-dot { animation: dotPulse 1.5s infinite; }
.alert-title strong { color: #d32f2f; font-size: 16px; }

.alert-details { display: flex; flex-direction: column; gap: 8px; }
.detail-row { display: flex; align-items: flex-start; font-size: 13px; line-height: 1.6;}
.detail-label { color: #7d94b7; min-width: 70px; }
.detail-value { color: #17376f; font-weight: 500; flex: 1;}
.danger-text { color: #e53935 !important; font-weight: bold !important;}

.analysis-action {
  background: linear-gradient(180deg, #f8fbff, #f4f8ff);
  border-top: 1px dashed rgba(28, 61, 144, 0.08);
  padding: 20px 20px 24px 20px;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 1;
  justify-content: center;
}
.action-desc {
  font-size: 13px;
  color: #5d7192;
  margin: 0 0 16px 0;
  line-height: 1.6;
}

/* 呼吸发光按钮 */
@keyframes pulseBreath {
  0% { box-shadow: 0 0 0 0 rgba(28, 100, 242, 0.3); }
  70% { box-shadow: 0 0 0 12px rgba(28, 100, 242, 0); }
  100% { box-shadow: 0 0 0 0 rgba(28, 100, 242, 0); }
}
.pulse-btn { animation: pulseBreath 2s infinite; }
.pulse-btn:hover { animation: none; transform: translateY(-2px); box-shadow: 0 10px 24px rgba(28, 100, 242, 0.3);}
.large-btn { padding: 10px 24px; font-size: 14px; border-radius: 999px; }

/* 研判结论区 (变成独立气泡) */
.judgment-card {
  flex: 1;
  background: #fff;
  opacity: 0.5;
  transition: all 0.5s ease;
}
.judgment-card.show { opacity: 1; }
.judgment-content { display: flex; flex-direction: column; gap: 12px; padding: 10px 20px 20px 20px;}
.judge-item {
  display: flex; flex-direction: column; gap: 6px;
  font-size: 13px;
  background: #f8fbff;
  padding: 14px 16px;
  border-radius: 16px; /* 卡片内卡片 */
  border: 1px solid rgba(28, 61, 144, 0.04);
  transition: transform 0.2s ease;
}
.judge-item:hover { transform: translateX(2px); background: #f0f7ff;}
.judge-item .label { color: #5d7192; font-weight: bold; font-size: 12px; }
.judge-item .value { color: #17376f; line-height: 1.5; font-size: 13.5px;}
.highlight-text { color: #e53935 !important; font-weight: bold; }
.action-text { color: #1c64f2 !important; font-weight: bold; }
.empty-text { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100%; color: #909399; font-size: 13px; }
.empty-placeholder-icon { font-size: 32px; margin-bottom: 10px; opacity: 0.5; filter: grayscale(1);}

/* ==== 模块一：右侧报告区 ==== */
.report-card { background: #fff; position: relative; }
.sticky-header { position: sticky; top: 0; background: rgba(255,255,255,0.9); backdrop-filter: blur(8px); }
.report-time { font-size: 12px; color: #909399; font-weight: normal;}

.scrollable-report-body {
  flex: 1;
  overflow-y: auto;
  padding: 10px 24px 24px 24px;
}

.empty-state, .loading-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  min-height: 400px;
  color: #7d94b7;
}
.empty-img { font-size: 48px; margin-bottom: 16px; opacity: 0.3; filter: grayscale(1); }

/* 优雅加载动画 */
.spinner {
  width: 44px; height: 44px;
  border: 3px solid rgba(28, 100, 242, 0.1); border-top: 3px solid #1c64f2;
  border-radius: 50%;
  animation: spin 1s cubic-bezier(0.55, 0.15, 0.45, 0.85) infinite;
  margin-bottom: 20px;
}
@keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
.loading-bar { width: 240px; height: 6px; background: rgba(28, 61, 144, 0.05); border-radius: 3px; margin-top: 16px; overflow: hidden; }
.progress { width: 40%; height: 100%; background: linear-gradient(90deg, #1c64f2, #4facfe); border-radius: 3px; animation: load 1.5s infinite ease-in-out; }
@keyframes load { 0% { transform: translateX(-100%); } 100% { transform: translateX(300%); } }

.slide-in { animation: slideIn 0.6s cubic-bezier(0.16, 1, 0.3, 1); }
@keyframes slideIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

/* 结构化报告详情 */
.report-section { margin-bottom: 30px; }
.section-tag {
  font-size: 15px;
  font-weight: bold;
  color: #17376f;
  margin-bottom: 16px;
  display: flex;
  align-items: center;
}
.tag-dot { width: 6px; height: 16px; background: #1c64f2; border-radius: 3px; margin-right: 10px; }

/* 类比匹配条目 (胶囊感) */
.analogy-list { display: flex; flex-direction: column; gap: 14px; }
.analogy-item {
  background: rgba(248, 251, 255, 0.6);
  border: 1px solid rgba(28, 61, 144, 0.05);
  border-radius: 16px;
  padding: 16px 20px;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  box-shadow: 0 4px 16px rgba(0,0,0,0.01);
}
.analogy-item:hover { transform: translateY(-2px); background: #f0f7ff; box-shadow: 0 8px 24px rgba(28,61,144,0.05);}
.analogy-info { display: flex; justify-content: space-between; align-items: center; margin-bottom: 14px; }
.case-name { font-size: 14px; font-weight: bold; color: #1c64f2; cursor: pointer; }
.case-name:hover { text-decoration: underline; color: #096dd9; }
.case-effect.success { font-size: 12px; color: #52c41a; background: #f6ffed; padding: 4px 12px; border: 1px solid #b7eb8f; border-radius: 999px; font-weight: 500;}

.match-bar-container { display: flex; align-items: center; font-size: 12px; color: #5d7192; }
.match-track { flex: 1; height: 8px; background: rgba(28, 61, 144, 0.06); border-radius: 999px; margin: 0 16px; overflow: hidden; }
.match-fill { height: 100%; background: linear-gradient(90deg, #4facfe, #1c64f2); border-radius: 999px; }
.match-percent { font-weight: bold; color: #1c64f2; width: 35px; text-align: right; font-size: 14px;}

.mt-15 { margin-top: 36px; }

/* 综合建议 - 全新视觉分块 Card-in-Card */
.section-content-grid {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.advice-card {
  padding: 20px 24px;
  border-radius: 16px;
  border: 1px solid transparent;
}
.advice-card h4 { margin: 0 0 12px 0; font-size: 14px; color: #17376f; display: flex; align-items: center; }
.block-icon { margin-right: 8px; font-size: 16px; }
.advice-card p, .plan-desc { margin: 0; font-size: 13.5px; color: #5f728f; line-height: 1.7; }
.plan-desc b { color: #17376f; font-weight: bold;}

.neutral-card { background: #f8fbff; border-color: rgba(28, 61, 144, 0.04); }
.warning-card { background: #fffcf0; border-color: rgba(251, 192, 45, 0.15); }
.info-card { background: #fdfdfe; border-color: rgba(28, 61, 144, 0.05); }

.plan-item { display: flex; flex-direction: column; gap: 8px; margin-bottom: 4px; }
.plan-badge { display: inline-block; padding: 4px 10px; border-radius: 6px; font-size: 12px; font-weight: bold; width: fit-content;}
.primary-badge { background: #fff0d1; color: #d97706; }
.secondary-badge { background: #eaf2ff; color: #1c64f2; }
.plan-divider { height: 1px; background: rgba(0,0,0,0.04); margin: 12px 0; }

/* 优化后的列表点 */
.styled-points-list { list-style: none; padding: 0; margin: 0; }
.styled-points-list li { position: relative; padding-left: 18px; margin-bottom: 10px; font-size: 13.5px; color: #5f728f; line-height: 1.6;}
.styled-points-list li:last-child { margin-bottom: 0; }
.styled-points-list li::before { content: ''; position: absolute; left: 0; top: 7px; width: 6px; height: 6px; border-radius: 50%; background: #1c64f2; opacity: 0.8;}

.report-actions { display: flex; justify-content: center; gap: 16px; margin-top: 32px; padding-top: 24px; border-top: 1px solid rgba(28, 61, 144, 0.06); }

/* ==== 模块二：专家库与规则库视图 ==== */
.sub-tab-container { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
.sub-tabs { display: flex; gap: 24px; border-bottom: 2px solid rgba(28, 61, 144, 0.04); }
.sub-tab { padding: 12px 6px; font-size: 15px; color: #5d7192; cursor: pointer; border-bottom: 3px solid transparent; margin-bottom: -2px; transition: all 0.3s ease; }
.sub-tab:hover { color: #1c64f2; }
.sub-tab.active { color: #1c64f2; font-weight: bold; border-bottom-color: #1c64f2; }

.toolbar-actions { display: flex; gap: 16px; }
.search-box { display: flex; width: 320px; box-shadow: 0 2px 8px rgba(0,0,0,0.02); border-radius: 999px;}
.search-input { border-radius: 999px 0 0 999px; border-right: none; padding-left: 16px; background: #f8fbff;}
.search-input:focus { background: #fff;}
.icon-btn { border-radius: 0 999px 999px 0; padding: 0 16px; }

/* 表格区域美化 */
.table-container { flex: 1; overflow-y: auto; border: 1px solid rgba(28, 61, 144, 0.06); border-radius: 16px; box-shadow: 0 4px 12px rgba(0,0,0,0.01);}
.custom-table { width: 100%; border-collapse: collapse; font-size: 13px; text-align: left; }
.custom-table th { background: linear-gradient(180deg, #f8fbff, #f0f4f8); padding: 14px 16px; color: #5d7192; font-weight: bold; border-bottom: 2px solid rgba(28, 61, 144, 0.04); position: sticky; top: 0; z-index: 10; }
.custom-table td { padding: 14px 16px; border-bottom: 1px solid rgba(28, 61, 144, 0.04); color: #444; vertical-align: middle; line-height: 1.6; transition: background 0.2s;}
.custom-table tbody tr:hover { background: #f4f8ff; }

.case-id { font-weight: bold; color: #1c64f2; font-family: monospace; font-size: 14px; }
.rule-id { font-weight: bold; color: #f57c00; font-family: monospace; font-size: 14px; }
.rule-condition { color: #17376f; font-weight: 500; }
.rule-param { color: #1d8e5a; font-family: monospace; font-size: 12px;}

/* 多维标签体系样式 (变圆钝) */
.tags-container { display: flex; flex-wrap: wrap; gap: 8px; }
.dim-tag { display: inline-block; padding: 4px 10px; border-radius: 999px; font-size: 11.5px; border: 1px solid transparent; font-weight: 500;}
.dim-tag.type { background: #eef5fe; color: #1890ff; border-color: #b3d8ff; }
.dim-tag.rock { background: #fff5e6; color: #d46b08; border-color: #ffd591; }
.dim-tag.trigger { background: #f6ffed; color: #389e0d; border-color: #b7eb8f; }
.dim-tag.method { background: #f9f0ff; color: #531dab; border-color: #d3adf7; }

.status-tag { display: inline-block; padding: 4px 12px; border-radius: 999px; font-size: 12px; border: 1px solid transparent; font-weight: bold;}
.status-tag.success { color: #52c41a; background: #f6ffed; border-color: #b7eb8f; }
.status-tag.active { color: #1890ff; background: #e6f7ff; border-color: #91d5ff; }
.status-tag.inactive { color: #909399; background: #f4f4f5; border-color: #e9e9eb; }

.truncate-text {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  text-overflow: ellipsis;
  color: #5f728f;
}
.action-buttons { display: flex; gap: 12px; }

/* ==== 呼吸感按钮与输入框组件 ==== */
.primary-btn {
  background: linear-gradient(135deg, #1c64f2, #163c8f);
  color: #fff; border: none; padding: 8px 20px;
  border-radius: 999px; font-size: 13px; font-weight: 600;
  cursor: pointer; transition: all 0.3s ease;
  display: inline-flex; align-items: center; justify-content: center;
  box-shadow: 0 6px 16px rgba(28, 100, 242, 0.2);
}
.primary-btn:hover { transform: translateY(-1px); box-shadow: 0 8px 20px rgba(28, 100, 242, 0.3); }
.primary-btn:disabled { background: #a8b4d0; box-shadow: none; transform: none; cursor: not-allowed; }

.secondary-btn {
  background: #fff; color: #1c3d90;
  border: 1px solid rgba(28, 61, 144, 0.16);
  padding: 8px 20px; border-radius: 999px;
  font-size: 13px; font-weight: 600; cursor: pointer; transition: all 0.3s ease;
}
.secondary-btn:hover { border-color: #1c64f2; background: #f8fbff; transform: translateY(-1px); }

.text-btn { background: none; border: none; color: #1890ff; cursor: pointer; font-size: 13px; padding: 0; transition: color 0.3s;}
.text-btn:hover { text-decoration: underline; color: #096dd9; }
.danger-text { color: #e53935 !important; }
.danger-text:hover { color: #c62828 !important; }

/* 表单输入框圆钝化 */
.ctrl-input {
  padding: 10px 14px;
  border: 1px solid rgba(28, 61, 144, 0.12);
  border-radius: 12px;
  outline: none; font-size: 13px; width: 100%;
  box-sizing: border-box; background: #fcfcfd;
  transition: all 0.3s ease;
}
.ctrl-input:focus { border-color: #1c64f2; background: #fff; box-shadow: 0 0 0 3px rgba(28, 100, 242, 0.1); }
.ctrl-input:disabled { background: #f5f7fa; color: #909399; cursor: not-allowed; border-color: rgba(28, 61, 144, 0.05); }

/* ==== 模态框 ==== */
.modal-overlay {
  position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
  background: rgba(7, 18, 34, 0.4);
  display: flex; align-items: center; justify-content: center;
  z-index: 9999; backdrop-filter: blur(4px);
}
.modal-box {
  background: #fff; width: 740px;
  border-radius: 24px;
  box-shadow: 0 20px 60px rgba(0,0,0,0.15);
  display: flex; flex-direction: column;
  animation: slideInModal 0.3s cubic-bezier(0.16, 1, 0.3, 1);
  overflow: hidden;
}
.rule-modal-box { width: 580px; }
@keyframes slideInModal { from { opacity: 0; transform: translateY(30px) scale(0.98); } to { opacity: 1; transform: translateY(0) scale(1); } }

.modal-header {
  display: flex; justify-content: space-between; align-items: center;
  padding: 20px 24px;
  border-bottom: 1px solid rgba(28, 61, 144, 0.04);
  background: linear-gradient(180deg, #f8fbff, #fff);
}
.modal-header h3 { margin: 0; font-size: 16px; color: #17376f; font-weight: bold;}
.close-btn {
  cursor: pointer; font-size: 18px; color: #7d94b7;
  transition: all 0.3s; width: 32px; height: 32px;
  display: flex; align-items: center; justify-content: center;
  border-radius: 50%; background: #f0f4f8;
}
.close-btn:hover { color: #fff; background: #e53935; transform: rotate(90deg); }

.scrollable-modal { padding: 24px; max-height: 65vh; overflow-y: auto; background: #fafbfc;}

.form-section-title { font-size: 14px; font-weight: bold; color: #17376f; margin-bottom: 16px; border-left: 4px solid #1c64f2; padding-left: 10px; border-radius: 2px;}
.form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 24px;}
.single-col { grid-template-columns: 1fr; gap: 16px;}
.form-item { display: flex; flex-direction: column; gap: 8px; }
.form-item.full-width { grid-column: span 2; }
.form-item label { font-size: 13px; color: #5d7192; font-weight: bold; }
.req { color: #e53935; margin-left: 4px; }
textarea.ctrl-input { resize: vertical; font-family: inherit; line-height: 1.5;}

.modal-footer {
  padding: 16px 24px;
  border-top: 1px solid rgba(28, 61, 144, 0.06);
  display: flex; justify-content: flex-end; gap: 16px;
  background: #fff;
}

/* 滚动条美化 */
::-webkit-scrollbar { width: 8px; height: 8px; }
::-webkit-scrollbar-thumb { border-radius: 999px; background: rgba(92, 128, 190, 0.25); }
::-webkit-scrollbar-track { background: transparent; }
</style>