<template>
  <div class="view-container">
    <aside class="sidebar">
      <div class="sidebar-header">功能菜单</div>
      <ul class="nav-list">
        <li :class="{ active: currentTab === 'work-order' }" @click="currentTab = 'work-order'">
          预警闭环管理
        </li>
        <li :class="{ active: currentTab === 'rules' }" @click="currentTab = 'rules'">
          通知规则配置
        </li>
        <li :class="{ active: currentTab === 'contacts' }" @click="currentTab = 'contacts'">
          层级化联系人
        </li>
      </ul>
    </aside>

    <main class="main-content">
      <div v-if="currentTab === 'work-order'">
        <h2 class="page-title">预警闭警处理管理</h2>

        <div class="sys-card timeline-card">
          <div class="card-header">
            <h3>时间轴（预警推送及处理进程）</h3>
          </div>
          <div class="timeline-wrapper">
            <div class="timeline-step completed"><div class="step-node"></div><div class="step-label">自动推送</div></div>
            <div class="timeline-line"></div>
            <div class="timeline-step active"><div class="step-node"></div><div class="step-label">多方会商</div></div>
            <div class="timeline-line"></div>
            <div class="timeline-step"><div class="step-node"></div><div class="step-label">隐患处理</div></div>
            <div class="timeline-line"></div>
            <div class="timeline-step"><div class="step-node"></div><div class="step-label">跟踪归档</div></div>
          </div>
        </div>

        <div class="sys-card table-card">
          <div class="table-header">
            <h3>预警记录管理</h3>
            <div class="header-tools">
              <button class="add-btn" @click="handleCreateOrder">+ 新增处置单</button>
            </div>
          </div>
          <table class="sys-table">
            <thead>
            <tr>
              <th>预警时间</th><th>预警等级</th><th>预警区域</th><th>处理人员</th><th>处置状态</th><th>是否闭环</th><th>后备处理人员</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="(item, index) in tableData" :key="index">
              <td>{{ item.time }}</td>
              <td><span :class="['level-tag', item.levelClass]">{{ item.levelText }}</span></td>
              <td>{{ item.area }}</td>
              <td>{{ item.staff }}</td>
              <td><span :class="getStatusClass(item.status)">{{ item.status }}</span></td>
              <td>{{ item.isClosed }}</td>
              <td>{{ item.backupStaff }}</td>
            </tr>
            </tbody>
          </table>
        </div>
      </div>

      <div v-if="currentTab === 'rules'">
        <h2 class="page-title">通知规则配置</h2>
        <div class="sys-card">
          <div class="table-header"><h3>推荐的核心通知规则</h3></div>
          <table class="sys-table">
            <thead>
            <tr>
              <th width="150">预警等级</th><th>通知范围 (建议)</th><th width="250">通知渠道</th>
            </tr>
            </thead>
            <tbody>
            <tr>
              <td><span class="level-tag blue">蓝色预警 (四级)</span></td>
              <td>1. 现场与执行层：相关班组安全员、调度室值班人员。<br>2. 矿区技术与管理层：边坡业务主管部门、安监部。</td>
              <td>系统内消息 + 企业微信推送</td>
            </tr>
            <tr>
              <td><span class="level-tag yellow">黄色预警 (三级)</span></td>
              <td>涵盖蓝色预警所有人员，并增加：<br>矿区决策层：煤矿总工程师、总经理（矿长）。</td>
              <td>系统消息 + 企业微信 + 短信 (矿区决策层)</td>
            </tr>
            <tr>
              <td><span class="level-tag orange">橙色预警 (二级)</span></td>
              <td>涵盖黄色预警所有人员，并增加：<br>1. 煤矿领导层；2. 上级公司业务监管部门；3. 政府监管部门。</td>
              <td>企业微信 + 短信 + 自动语音电话 (关键负责人)</td>
            </tr>
            <tr>
              <td><span class="level-tag red">红色预警 (一级)</span></td>
              <td>涵盖橙色预警所有人员，并增加：<br>集团应急指挥层：内蒙古公司董事长、总调度室。</td>
              <td>企业微信 + 短信 + 语音电话 (全面启动) + 上级系统自动上报</td>
            </tr>
            </tbody>
          </table>
        </div>
      </div>

      <div v-if="currentTab === 'contacts'">
        <h2 class="page-title">层级化联系人管理</h2>
        <div class="contact-container">
          <div class="sys-card tree-sidebar">
            <div class="card-header"><h3>矿山组织架构树</h3></div>
            <div class="org-tree-mock">
              <div>▼ 内蒙古能源有限公司</div>
              <div style="padding-left: 20px;">▼ 露天煤矿</div>
              <div style="padding-left: 40px;">- 生产技术部</div>
              <div style="padding-left: 40px;">- 安全监察部</div>
              <div style="padding-left: 40px; color: #1c3d90; font-weight: bold;">- 调度指挥中心</div>
            </div>
          </div>
          <div class="sys-card list-content">
            <div class="table-header">
              <h3>人员联系方式与角色配置</h3>
              <div class="header-tools">
                <button class="add-btn" @click="handleCreateContact">+ 新增联系人</button>
              </div>
            </div>
            <table class="sys-table">
              <thead>
              <tr>
                <th>姓名</th><th>系统账号</th><th>手机号码</th><th>所属部门</th><th>预警通知链角色</th>
              </tr>
              </thead>
              <tbody>
              <tr v-for="(person, index) in contactData" :key="index">
                <td>{{ person.name }}</td>
                <td>{{ person.account }}</td>
                <td>{{ person.phone }}</td>
                <td>{{ person.dept }}</td>
                <td><span :class="['role-tag', person.roleClass]">{{ person.role }}</span></td>
              </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <div v-if="showModal" class="modal-overlay">
        <div class="modal-content">
          <div class="modal-header">
            <h3>新增预警处置单</h3>
            <span class="close-btn" @click="showModal = false">×</span>
          </div>
          <div class="modal-body">
            <div class="form-grid">
              <div class="form-item">
                <label>预警时间</label>
                <input type="datetime-local" v-model="formData.time">
              </div>
              <div class="form-item">
                <label>预警等级</label>
                <select v-model="formData.level">
                  <option value="red">红色预警 (一级)</option>
                  <option value="orange">橙色预警 (二级)</option>
                  <option value="yellow">黄色预警 (三级)</option>
                  <option value="blue">蓝色预警 (四级)</option>
                </select>
              </div>
              <div class="form-item">
                <label>预警区域</label>
                <input type="text" v-model="formData.area" placeholder="请输入区域名称">
              </div>
              <div class="form-item">
                <label>处理人员</label>
                <input type="text" v-model="formData.staff" placeholder="请输入姓名">
              </div>
              <div class="form-item">
                <label>处置状态</label>
                <input type="text" v-model="formData.status" placeholder="例如：会商中、待处理">
              </div>
              <div class="form-item">
                <label>是否闭环</label>
                <select v-model="formData.isClosed">
                  <option value="是">是</option>
                  <option value="否">否</option>
                </select>
              </div>
              <div class="form-item full-width">
                <label>后备处理人员</label>
                <input type="text" v-model="formData.backupStaff" placeholder="请输入后备人员姓名">
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button class="btn-cancel" @click="showModal = false">取消</button>
            <button class="btn-confirm" @click="submitForm">确定添加</button>
          </div>
        </div>
      </div>

      <div v-if="showContactModal" class="modal-overlay">
        <div class="modal-content">
          <div class="modal-header">
            <h3>新增联系人配置</h3>
            <span class="close-btn" @click="showContactModal = false">×</span>
          </div>
          <div class="modal-body">
            <div class="form-grid">
              <div class="form-item">
                <label>姓名</label>
                <input type="text" v-model="contactForm.name" placeholder="姓名">
              </div>
              <div class="form-item">
                <label>系统账号</label>
                <input type="text" v-model="contactForm.account" placeholder="系统账号">
              </div>
              <div class="form-item">
                <label>手机号码</label>
                <input type="text" v-model="contactForm.phone" placeholder="手机号码">
              </div>
              <div class="form-item">
                <label>所属部门</label>
                <input type="text" v-model="contactForm.dept" placeholder="例如：调度指挥中心">
              </div>
              <div class="form-item full-width">
                <label>预警通知链角色</label>
                <select v-model="contactForm.role">
                  <option value="关键负责人">关键负责人</option>
                  <option value="技术支持">技术支持</option>
                  <option value="安全监督">安全监督</option>
                  <option value="现场人员">现场人员</option>
                </select>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button class="btn-cancel" @click="showContactModal = false">取消</button>
            <button class="btn-confirm" @click="submitContactForm">确定添加</button>
          </div>
        </div>
      </div>

    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentTab: 'work-order',
      showModal: false,
      showContactModal: false, // 联系人弹窗开关
      tableData: [
        { time: '2026-03-30 09:15', levelText: '红色预警 (一级)', levelClass: 'red', area: '北边坡 GNSS-03', staff: '张经理 (总调度)', status: '会商中', isClosed: '否', backupStaff: '王工' },
        { time: '2026-03-30 08:30', levelText: '黄色预警 (三级)', levelClass: 'yellow', area: '东部采区区段', staff: '孙主任 (安监部)', status: '待处理', isClosed: '否', backupStaff: '周工' },
        { time: '2026-03-30 08:00', levelText: '橙色预警 (二级)', levelClass: 'orange', area: '排水口 Water-01', staff: '李科长 (技术部)', status: '处理中', isClosed: '否', backupStaff: '赵技术员' },
        { time: '2026-03-29 14:20', levelText: '蓝色预警 (四级)', levelClass: 'blue', area: '西区选煤厂', staff: '王大力', status: '已完成', isClosed: '是', backupStaff: '-' }
      ],
      // 联系人动态数据
      contactData: [
        { name: '张经理', account: 'admin_01', phone: '138****0001', dept: '调度指挥中心', role: '关键负责人', roleClass: '' },
        { name: '王工', account: 'tech_02', phone: '139****0002', dept: '生产技术部', role: '技术支持', roleClass: 'secondary' }
      ],
      formData: {
        time: '', level: 'red', area: '', staff: '', status: '', isClosed: '否', backupStaff: ''
      },
      // 联系人表单
      contactForm: {
        name: '', account: '', phone: '', dept: '', role: '关键负责人'
      }
    };
  },
  methods: {
    handleCreateOrder() {
      const now = new Date();
      const offset = now.getTimezoneOffset() * 60000;
      this.formData.time = new Date(now - offset).toISOString().slice(0, 16);
      this.showModal = true;
    },
    handleCreateContact() {
      // 重置并打开联系人弹窗
      this.contactForm = { name: '', account: '', phone: '', dept: '', role: '关键负责人' };
      this.showContactModal = true;
    },
    submitForm() {
      const levelMap = { 'red': '红色预警 (一级)', 'orange': '橙色预警 (二级)', 'yellow': '黄色预警 (三级)', 'blue': '蓝色预警 (四级)' };
      const newEntry = {
        time: this.formData.time.replace('T', ' '),
        levelText: levelMap[this.formData.level],
        levelClass: this.formData.level,
        area: this.formData.area || '未指定区域',
        staff: this.formData.staff || '待分配',
        status: this.formData.status || '待处理',
        isClosed: this.formData.isClosed,
        backupStaff: this.formData.backupStaff || '-'
      };
      this.tableData.unshift(newEntry);
      this.showModal = false;
      this.formData.area = ''; this.formData.staff = ''; this.formData.status = '';
    },
    submitContactForm() {
      // 提交联系人数据
      const newContact = {
        name: this.contactForm.name || '未命名',
        account: this.contactForm.account || 'N/A',
        phone: this.contactForm.phone || 'N/A',
        dept: this.contactForm.dept || '未定部门',
        role: this.contactForm.role,
        roleClass: this.contactForm.role === '关键负责人' ? '' : 'secondary'
      };
      this.contactData.push(newContact);
      this.showContactModal = false;
    },
    getStatusClass(status) {
      if (status === '已完成') return 'status-done';
      if (status === '待处理') return 'status-pending';
      return 'status-process';
    }
  }
};
</script>

<style scoped>
/* 基础布局 */
.view-container { display: flex; min-height: 100vh; background: #f4f7fa; }
.sidebar { width: 200px; background: #1c3d90; color: #fff; }
.sidebar-header { padding: 20px; font-weight: bold; border-bottom: 1px solid rgba(255,255,255,0.1); background: rgba(0,0,0,0.1); }
.nav-list { list-style: none; padding: 0; }
.nav-list li { padding: 15px 20px; cursor: pointer; font-size: 14px; transition: 0.3s; border-left: 4px solid transparent; }
.nav-list li:hover { background: #2a4da8; }
.nav-list li.active { background: #2a4da8; border-left: 4px solid #4facfe; font-weight: bold; }
.main-content { flex: 1; padding: 20px; position: relative; }
.page-title { margin-bottom: 20px; color: #1c3d90; font-size: 1.5rem; }

/* 通用卡片样式 */
.sys-card { background: #fff; border-radius: 4px; box-shadow: 0 2px 10px rgba(0,0,0,0.05); margin-bottom: 20px; overflow: hidden;}
.card-header, .table-header { padding: 15px 20px; border-bottom: 1px solid #eee; display: flex; justify-content: space-between; align-items: center; }

/* 表格与标签 */
.sys-table { width: 100%; border-collapse: collapse; font-size: 13px; }
.sys-table th, .sys-table td { text-align: left; padding: 14px 20px; border-bottom: 1px solid #eee; }
.sys-table thead { background: #F8FBFF; color: #666; }
.level-tag { padding: 4px 8px; border-radius: 4px; font-size: 12px; font-weight: bold; }
.level-tag.red { background: #fee2e2; color: #ef4444; border: 1px solid #fecaca; }
.level-tag.orange { background: #ffedd5; color: #f97316; border: 1px solid #fed7aa; }
.level-tag.yellow { background: #fef9c3; color: #ca8a04; border: 1px solid #fde047; }
.level-tag.blue { background: #dbeafe; color: #3b82f6; border: 1px solid #bfdbfe; }
.status-process { color: #f39c12; }
.status-pending { color: #94a3b8; }
.status-done { color: #27ae60; font-weight: bold; }
.add-btn { background: #1c3d90; color: #fff; border: none; padding: 8px 16px; border-radius: 4px; cursor: pointer; }

/* 弹窗样式 */
.modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); display: flex; align-items: center; justify-content: center; z-index: 1000; }
.modal-content { background: #fff; width: 500px; border-radius: 8px; box-shadow: 0 5px 20px rgba(0,0,0,0.2); animation: slideDown 0.3s ease-out; }
@keyframes slideDown { from { transform: translateY(-30px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
.modal-header { padding: 15px 20px; border-bottom: 1px solid #eee; display: flex; justify-content: space-between; align-items: center; }
.modal-header h3 { font-size: 16px; color: #1c3d90; }
.close-btn { cursor: pointer; font-size: 20px; color: #999; }
.modal-body { padding: 20px; }
.form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
.form-item { display: flex; flex-direction: column; }
.form-item.full-width { grid-column: span 2; }
.form-item label { font-size: 13px; color: #666; margin-bottom: 6px; }
.form-item input, .form-item select { padding: 8px; border: 1px solid #ddd; border-radius: 4px; font-size: 13px; }
.modal-footer { padding: 15px 20px; border-top: 1px solid #eee; text-align: right; }
.btn-cancel { background: #f5f5f5; border: 1px solid #ddd; padding: 8px 20px; border-radius: 4px; cursor: pointer; margin-right: 10px; color: #666; }
.btn-confirm { background: #1c3d90; color: #fff; border: none; padding: 8px 20px; border-radius: 4px; cursor: pointer; }

/* 时间轴 */
.timeline-wrapper { display: flex; align-items: center; justify-content: center; padding: 40px 20px 20px; }
.timeline-step { display: flex; flex-direction: column; align-items: center; position: relative; }
.step-node { width: 16px; height: 16px; border-radius: 50%; background: #ddd; border: 4px solid #fff; box-shadow: 0 0 0 2px #ddd; z-index: 2; }
.step-label { margin-top: 10px; font-size: 13px; color: #666; }
.timeline-line { flex: 1; height: 2px; background: #ddd; max-width: 100px; margin: 0 -5px 25px; }
.completed .step-node, .completed + .timeline-line { background: #27ae60; box-shadow: 0 0 0 2px #27ae60; }
.active .step-node { background: #1c3d90; box-shadow: 0 0 0 2px #1c3d90; }

/* 联系人布局 */
.contact-container { display: flex; gap: 20px; }
.tree-sidebar { width: 250px; }
.list-content { flex: 1; }
.org-tree-mock { padding: 20px; line-height: 2; font-size: 14px; color: #666; }
.role-tag { background: #e1f5fe; color: #0288d1; padding: 2px 8px; border-radius: 4px; font-size: 12px; }
.role-tag.secondary { background: #f5f5f5; color: #757575; }
</style>