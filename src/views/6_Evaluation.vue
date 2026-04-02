<template>
  <div class="view-page">
    <h2 class="page-title">系统配置与效能评估</h2>

    <section class="section-container">
      <div class="section-header">
        <h3>预警效能评估与优化</h3>
        <span class="subtitle">评估指标体系：准确性、及时性、处置效率</span>
      </div>

      <div class="kpi-grid">
        <div class="sys-card kpi-card">
          <h4>预警准确性 (综合)</h4>
          <div class="kpi-value">98.5%</div>
          <div class="kpi-detail">漏报率: 0.5% | 误报率: 1.0%</div>
        </div>
        <div class="sys-card kpi-card">
          <h4>预警及时性</h4>
          <div class="kpi-value">12 <span class="unit">分钟</span></div>
          <div class="kpi-detail">平均临滑预报提前量</div>
        </div>
        <div class="sys-card kpi-card">
          <h4>闭环处置效率</h4>
          <div class="kpi-value">2.5 <span class="unit">小时</span></div>
          <div class="kpi-detail">预警发出到隐患关闭平均时长</div>
        </div>
      </div>
    </section>

    <section class="section-container">
      <div class="section-header">
        <h3>预警参数设置</h3>
      </div>

      <div class="config-form">
        <div class="sys-card config-card">
          <div class="card-title">
            <i class="icon">📊</i> 样本容量设置
          </div>
          <div class="form-item">
            <label>历史数据时间窗口</label>
            <div class="input-with-unit">
              <input type="text" value="180">
              <span class="unit-tag">天</span>
            </div>
            <p class="form-tip warning">重要参数：影响趋势判断的稳定性与灵敏度</p>
          </div>
        </div>

        <div class="sys-card config-card">
          <div class="card-title">
            <i class="icon">🕒</i> 数据采集频率设置
          </div>
          <div class="form-item">
            <label>基准采集频率</label>
            <input type="text" value="1次/小时" class="full-width">
          </div>
          <div class="form-item dynamic-row">
            <div class="label-group">
              <label>动态调整频率</label>
              <p class="form-tip">平衡数据传输成本与预警实时性需求</p>
            </div>
            <div class="switch-control">
              <input type="checkbox" checked id="dynamic-switch">
              <label for="dynamic-switch"></label>
            </div>
          </div>
          <div class="dynamic-config-box">
            <span>当变形速率 ></span>
            <input type="text" value="0.5" class="mini-input">
            <span>mm/天时，自动提升采集频率至：</span>
            <input type="text" value="1次/30分钟" class="short-input">
          </div>
        </div>

        <div class="sys-card config-card">
          <div class="card-title">
            <i class="icon">⚖️</i> 显著性水平(α)设置
          </div>
          <div class="form-item">
            <label>显著性水平值</label>
            <input type="text" value="0.05" class="full-width">
            <div class="slider-container">
              <span class="slider-label">更严格</span>
              <input type="range" min="0.01" max="0.1" step="0.01" value="0.05">
              <span class="slider-label">更宽松</span>
            </div>
            <p class="form-tip">用于模型初始化或基准数据更新时的正态分布检验</p>
          </div>
        </div>

        <div class="form-actions">
          <button class="btn-secondary">× 取消</button>
          <button class="btn-outline">✓ 应用</button>
          <button class="btn-primary">💾 确定</button>
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped>
.view-page { padding: 20px; background: #f0f2f5; min-height: 100vh; }
.page-title { margin-bottom: 25px; color: #1c3d90; font-weight: bold; border-left: 4px solid #1c3d90; padding-left: 15px; }

.section-container { margin-bottom: 30px; }
.section-header { margin-bottom: 15px; display: flex; align-items: baseline; gap: 15px; }
.section-header h3 { font-size: 18px; color: #333; }
.subtitle { font-size: 13px; color: #666; }

/* KPI 样式 */
.kpi-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px; }
.kpi-card { padding: 20px; text-align: center; border-top: 3px solid #1c3d90; }
.kpi-card h4 { font-size: 14px; color: #666; margin-bottom: 10px; }
.kpi-value { font-size: 32px; font-weight: bold; color: #1c3d90; }
.kpi-value .unit { font-size: 16px; font-weight: normal; }
.kpi-detail { margin-top: 10px; font-size: 12px; color: #999; }

/* 配置表单样式 */
.config-form { display: flex; flex-direction: column; gap: 15px; max-width: 900px; margin: 0 auto; }
.config-card { padding: 20px; }
.card-title { font-weight: bold; color: #1c3d90; margin-bottom: 20px; display: flex; align-items: center; gap: 8px; border-bottom: 1px solid #f0f0f0; padding-bottom: 10px; }

.form-item { margin-bottom: 15px; }
.form-item label { display: block; font-size: 14px; margin-bottom: 8px; color: #333; font-weight: 500; }
.form-tip { font-size: 12px; color: #999; margin-top: 6px; }
.form-tip.warning { color: #f39c12; }

/* 输入控件 */
.input-with-unit { position: relative; width: 100%; }
.input-with-unit input, .full-width { width: 100%; padding: 10px; border: 1px solid #d9d9d9; border-radius: 4px; }
.unit-tag { position: absolute; right: 10px; top: 10px; color: #999; }

.dynamic-row { display: flex; justify-content: space-between; align-items: center; margin-top: 20px; }
.dynamic-config-box { background: #f9f9f9; padding: 15px; border-radius: 4px; display: flex; align-items: center; gap: 10px; font-size: 13px; margin-top: 10px; border: 1px dashed #ddd; }
.mini-input { width: 60px; padding: 5px; text-align: center; }
.short-input { width: 120px; padding: 5px; }

/* 滑块样式 */
.slider-container { display: flex; align-items: center; gap: 15px; margin-top: 15px; }
.slider-container input { flex: 1; accent-color: #1c3d90; }
.slider-label { font-size: 12px; color: #666; }

/* 开关样式 */
.switch-control input { display: none; }
.switch-control label { width: 44px; height: 22px; background: #ccc; display: block; border-radius: 11px; position: relative; cursor: pointer; }
.switch-control label::after { content: ''; width: 18px; height: 18px; background: #fff; border-radius: 50%; position: absolute; top: 2px; left: 2px; transition: 0.3s; }
.switch-control input:checked + label { background: #1c3d90; }
.switch-control input:checked + label::after { left: 24px; }

/* 按钮组 */
.form-actions { display: flex; justify-content: flex-end; gap: 12px; margin-top: 10px; }
.btn-primary { background: #1c3d90; color: #fff; border: none; padding: 10px 25px; border-radius: 4px; cursor: pointer; }
.btn-outline { background: #fff; color: #1c3d90; border: 1px solid #1c3d90; padding: 10px 25px; border-radius: 4px; cursor: pointer; }
.btn-secondary { background: #eee; color: #666; border: none; padding: 10px 25px; border-radius: 4px; cursor: pointer; }
</style>