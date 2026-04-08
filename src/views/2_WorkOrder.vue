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
      <div v-if="currentTab === 'work-order'" class="tab-pane-flex">
        <div class="page-header-wrap">
          <h2 class="page-title">预警闭环处理管理</h2>
        </div>

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
          <div class="table-scroll-wrap">
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
      </div>

      <div v-if="currentTab === 'rules'" class="tab-pane-flex">
        <div class="page-header-wrap">
          <h2 class="page-title">通知规则配置</h2>
        </div>
        <div class="sys-card flex-full-card">
          <div class="table-header"><h3>推荐的核心通知规则</h3></div>
          <div class="table-scroll-wrap">
            <table class="sys-table">
              <thead>
              <tr>
                <th width="150">预警等级</th><th>通知范围 (建议)</th><th width="450">通知渠道</th>
              </tr>
              </thead>
              <tbody>
              <tr>
                <td><span class="level-tag blue">蓝色预警 (四级)</span></td>
                <td class="td-desc">1. 现场与执行层：相关班组安全员、调度室值班人员。<br>2. 矿区技术与管理层：边坡业务主管部门、安监部。</td>
                <td>系统内消息 + 企业微信推送</td>
              </tr>
              <tr>
                <td><span class="level-tag yellow">黄色预警 (三级)</span></td>
                <td class="td-desc">涵盖蓝色预警所有人员，并增加：<br>矿区决策层：煤矿总工程师、总经理（矿长）。</td>
                <td>系统消息 + 企业微信 + 短信 (矿区决策层)</td>
              </tr>
              <tr>
                <td><span class="level-tag orange">橙色预警 (二级)</span></td>
                <td class="td-desc">涵盖黄色预警所有人员，并增加：<br>1. 煤矿领导层；2. 上级公司业务监管部门；3. 政府监管部门。</td>
                <td>企业微信 + 短信 + 自动语音电话 (关键负责人)</td>
              </tr>
              <tr>
                <td><span class="level-tag red">红色预警 (一级)</span></td>
                <td class="td-desc">涵盖橙色预警所有人员，并增加：<br>集团应急指挥层：内蒙古公司董事长、总调度室。</td>
                <td>企业微信 + 短信 + 语音电话 (全面启动) + 上级系统自动上报</td>
              </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <div v-if="currentTab === 'contacts'" class="tab-pane-flex">
        <div class="page-header-wrap">
          <h2 class="page-title">层级化联系人管理</h2>
        </div>
        <div class="contact-container">
          <div class="sys-card tree-sidebar">
            <div class="card-header"><h3>矿山组织架构树</h3></div>
            <div class="org-tree-mock">
              <div class="tree-node">▼ 内蒙古能源有限公司</div>
              <div class="tree-node" style="padding-left: 20px;">▼ 露天煤矿</div>
              <div class="tree-node" style="padding-left: 40px;">- 生产技术部</div>
              <div class="tree-node" style="padding-left: 40px;">- 安全监察部</div>
              <div class="tree-node active-node" style="padding-left: 40px;">- 调度指挥中心</div>
            </div>
          </div>
          <div class="sys-card list-content">
            <div class="table-header">
              <h3>人员联系方式与角色配置</h3>
              <div class="header-tools">
                <button class="add-btn" @click="handleCreateContact">+ 新增联系人</button>
              </div>
            </div>
            <div class="table-scroll-wrap">
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
                <input class="ctrl-input" type="datetime-local" v-model="formData.time">
              </div>
              <div class="form-item">
                <label>预警等级</label>
                <select class="ctrl-input" v-model="formData.level">
                  <option value="red">红色预警 (一级)</option>
                  <option value="orange">橙色预警 (二级)</option>
                  <option value="yellow">黄色预警 (三级)</option>
                  <option value="blue">蓝色预警 (四级)</option>
                </select>
              </div>
              <div class="form-item">
                <label>预警区域</label>
                <input class="ctrl-input" type="text" v-model="formData.area" placeholder="请输入区域名称">
              </div>
              <div class="form-item">
                <label>处理人员</label>
                <input class="ctrl-input" type="text" v-model="formData.staff" placeholder="请输入姓名">
              </div>
              <div class="form-item">
                <label>处置状态</label>
                <input class="ctrl-input" type="text" v-model="formData.status" placeholder="例如：会商中、待处理">
              </div>
              <div class="form-item">
                <label>是否闭环</label>
                <select class="ctrl-input" v-model="formData.isClosed">
                  <option value="是">是</option>
                  <option value="否">否</option>
                </select>
              </div>
              <div class="form-item full-width">
                <label>后备处理人员</label>
                <input class="ctrl-input" type="text" v-model="formData.backupStaff" placeholder="请输入后备人员姓名">
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button class="btn-cancel" @click="showModal = false">取消</button>
            <button class="btn-confirm ctrl-btn" @click="submitForm">确定添加</button>
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
                <input class="ctrl-input" type="text" v-model="contactForm.name" placeholder="姓名">
              </div>
              <div class="form-item">
                <label>系统账号</label>
                <input class="ctrl-input" type="text" v-model="contactForm.account" placeholder="系统账号">
              </div>
              <div class="form-item">
                <label>手机号码</label>
                <input class="ctrl-input" type="text" v-model="contactForm.phone" placeholder="手机号码">
              </div>
              <div class="form-item">
                <label>所属部门</label>
                <input class="ctrl-input" type="text" v-model="contactForm.dept" placeholder="例如：调度指挥中心">
              </div>
              <div class="form-item full-width">
                <label>预警通知链角色</label>
                <select class="ctrl-input" v-model="contactForm.role">
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
            <button class="btn-confirm ctrl-btn" @click="submitContactForm">确定添加</button>
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
/* =========== 核心：单页满屏限制与全局排版 =========== */
.view-container {
  display: flex;
  flex-direction: column;
  height: 100%; /* 修改：由 100vh 改为 100% */
  max-height: calc(100vh - 60px); /* 修改：增加 calc 减去系统顶部导航栏的高度，避免溢出 */
  width: 100%;
  box-sizing: border-box;
  background: #f4f7fa;
  overflow: hidden; /* 防止最外层滚动 */
  padding: 15px;
  gap: 15px;
  font-family: "Microsoft YaHei", sans-serif;
}

/* =========== 顶部菜单：修改为横向导航栏样式 =========== */
.sidebar {
  width: 100%;
  background: #f8fbfd;
  border: 1px solid #dcdfe6;
  border-radius: 6px;
  box-shadow: 0 2px 12px 0 rgba(0,0,0,0.05);
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 10px 15px;
  flex-shrink: 0;
}
.sidebar-header {
  font-size: 15px;
  font-weight: bold;
  color: #1c3d90;
  border-left: 4px solid #1c3d90;
  padding-left: 8px;
  margin-right: 30px;
  line-height: 1;
}
.nav-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: row;
  gap: 15px;
}
.nav-list li {
  padding: 8px 15px;
  background: transparent;
  color: #606266;
  cursor: pointer;
  font-size: 13px;
  border-radius: 4px;
  text-align: center;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}
.nav-list li:hover {
  background: #eef5fe;
  color: #1c3d90;
}
.nav-list li.active {
  background: #1c3d90;
  color: #fff;
  font-weight: bold;
  box-shadow: 0 4px 8px rgba(28, 61, 144, 0.3);
}

/* =========== 主内容区：弹性盒与溢出控制 =========== */
.main-content {
  flex: 1;
  position: relative;
  min-width: 0; /* 阻止 Flex 子项撑破 */
  display: flex;
  flex-direction: column;
}

.tab-pane-flex {
  display: flex;
  flex-direction: column;
  height: 100%;
  gap: 15px;
  overflow: hidden; /* 保障内部布局稳定 */
}

/* 统一各类标题的风格 */
.page-header-wrap { flex-shrink: 0; }
.page-title, .card-header h3, .table-header h3 {
  margin: 0;
  font-size: 16px;
  color: #1c3d90;
  border-left: 4px solid #1c3d90;
  padding-left: 8px;
  font-weight: bold;
  line-height: 1;
  display: flex;
  align-items: center;
}

/* =========== 模块卡片：微阴影描边体系 =========== */
.sys-card {
  background: #fff;
  border-radius: 6px;
  border: 1px solid #dcdfe6;
  box-shadow: 0 2px 12px 0 rgba(0,0,0,0.02);
  display: flex;
  flex-direction: column;
  overflow: hidden;
}
.flex-full-card { flex: 1; min-height: 0; }
.timeline-card { flex-shrink: 0; }
.table-card { flex: 1; min-height: 0; }

.card-header, .table-header {
  padding: 12px 15px;
  border-bottom: 1px solid #ebeef5;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #fff;
  flex-shrink: 0;
}

/* =========== 表格区域：局部滚动与精致条纹 =========== */
.table-scroll-wrap {
  flex: 1;
  overflow-y: auto; /* 核心：只允许表格内部滚动，不影响全局布局 */
}

/* 自定义轻量级滚动条 */
.table-scroll-wrap::-webkit-scrollbar, .org-tree-mock::-webkit-scrollbar { width: 6px; height: 6px; }
.table-scroll-wrap::-webkit-scrollbar-thumb, .org-tree-mock::-webkit-scrollbar-thumb { background: #dcdfe6; border-radius: 3px; }
.table-scroll-wrap::-webkit-scrollbar-track, .org-tree-mock::-webkit-scrollbar-track { background: transparent; }

.sys-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 13px;
  text-align: left;
}
.sys-table th, .sys-table td {
  padding: 12px 15px;
  border-bottom: 1px solid #ebeef5;
  color: #333;
}
.sys-table thead {
  background: #f8fbff;
  position: sticky; /* 固定表头 */
  top: 0;
  z-index: 2;
}
.sys-table th {
  color: #1c3d90;
  font-weight: bold;
}
.sys-table tbody tr { transition: background 0.2s; }
.sys-table tbody tr:hover { background: #f0f7ff; }
.td-desc { line-height: 1.6; color: #555; }

/* =========== 标签与状态映射体系 =========== */
.level-tag {
  padding: 4px 10px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: bold;
  display: inline-flex;
  align-items: center;
  gap: 6px;
}
.level-tag::before {
  content: '';
  display: block;
  width: 6px;
  height: 6px;
  border-radius: 50%;
}
.level-tag.red { background: #fff1f0; color: #f5222d; border: 1px solid #ffccc7; }
.level-tag.red::before { background: #f5222d; }
.level-tag.orange { background: #fff7e6; color: #fa8c16; border: 1px solid #ffe7ba; }
.level-tag.orange::before { background: #fa8c16; }
.level-tag.yellow { background: #feffe6; color: #faad14; border: 1px solid #fffb8f; }
.level-tag.yellow::before { background: #faad14; }
.level-tag.blue { background: #e6f7ff; color: #1890ff; border: 1px solid #91d5ff; }
.level-tag.blue::before { background: #1890ff; }

.status-process { color: #fa8c16; font-weight: bold; }
.status-pending { color: #909399; }
.status-done { color: #52c41a; font-weight: bold; }

/* =========== 按钮交互 =========== */
.add-btn, .btn-confirm.ctrl-btn {
  background: #1c3d90;
  color: #fff;
  border: none;
  padding: 6px 15px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  font-weight: bold;
  transition: all 0.3s ease;
}
.add-btn:hover, .btn-confirm.ctrl-btn:hover {
  background: #2a4da8;
  box-shadow: 0 4px 8px rgba(28, 61, 144, 0.3);
}
.btn-cancel {
  background: #f5f5f5;
  border: 1px solid #dcdfe6;
  padding: 5px 15px;
  border-radius: 4px;
  cursor: pointer;
  margin-right: 10px;
  color: #606266;
  font-size: 12px;
  transition: all 0.3s;
}
.btn-cancel:hover { background: #e8e8e8; color: #333; }

/* =========== 呼吸感时间轴 =========== */
@keyframes breathe {
  0% { box-shadow: 0 0 0 0 rgba(28, 61, 144, 0.4); }
  70% { box-shadow: 0 0 0 8px rgba(28, 61, 144, 0); }
  100% { box-shadow: 0 0 0 0 rgba(28, 61, 144, 0); }
}

.timeline-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 25px 20px 20px;
}
.timeline-step {
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
}
.step-node {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: #dcdfe6;
  border: 3px solid #fff;
  box-shadow: 0 0 0 2px #dcdfe6;
  z-index: 2;
  transition: all 0.3s;
}
.step-label { margin-top: 8px; font-size: 12px; color: #606266; font-weight: bold; }
.timeline-line {
  flex: 1;
  height: 2px;
  background: #ebeef5;
  max-width: 120px;
  margin: 0 -5px 20px;
  transition: background 0.3s;
}
.completed .step-node, .completed + .timeline-line {
  background: #52c41a;
  box-shadow: 0 0 0 2px #52c41a;
}
.active .step-node {
  background: #1c3d90;
  box-shadow: 0 0 0 2px #1c3d90;
  animation: breathe 2s infinite ease-in-out; /* 呼吸动画植入 */
}
.active .step-label { color: #1c3d90; }

/* =========== 联系人组织树双栏布局 =========== */
.contact-container {
  display: flex;
  gap: 15px;
  flex: 1;
  min-height: 0;
}
.tree-sidebar {
  width: 240px;
  background: #fcfcfc;
  flex-shrink: 0;
}
.list-content {
  flex: 1;
}
.org-tree-mock {
  padding: 15px;
  line-height: 2.2;
  font-size: 13px;
  color: #606266;
  overflow-y: auto;
  flex: 1;
}
.tree-node {
  cursor: pointer;
  border-radius: 4px;
  padding: 2px 6px;
  transition: all 0.2s;
}
.tree-node:hover {
  background: #f0f7ff;
  color: #1c3d90;
}
.active-node {
  color: #1c3d90;
  font-weight: bold;
  background: #eef5fe;
}
.role-tag {
  background: #eef5fe;
  color: #1c3d90;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
  border: 1px solid #c3e4fd;
}
.role-tag.secondary {
  background: #f4f4f5;
  color: #909399;
  border: 1px solid #e9e9eb;
}

/* =========== 弹窗组件：与过滤器输入框同源样式 =========== */
.modal-overlay {
  position: fixed;
  top: 0; left: 0;
  width: 100%; height: 100%;
  background: rgba(0,0,0,0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}
.modal-content {
  background: #fff;
  width: 520px;
  border-radius: 6px;
  box-shadow: 0 5px 20px rgba(0,0,0,0.2);
  animation: slideDown 0.3s ease-out;
}
@keyframes slideDown {
  from { transform: translateY(-20px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}
.modal-header {
  padding: 15px 20px;
  border-bottom: 1px solid #ebeef5;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.modal-header h3 {
  margin: 0;
  font-size: 15px;
  color: #1c3d90;
  font-weight: bold;
  border-left: 4px solid #1c3d90;
  padding-left: 8px;
}
.close-btn {
  cursor: pointer;
  font-size: 20px;
  color: #909399;
  transition: color 0.3s;
}
.close-btn:hover { color: #f5222d; }
.modal-body { padding: 20px; }
.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
}
.form-item { display: flex; flex-direction: column; }
.form-item.full-width { grid-column: span 2; }
.form-item label {
  font-size: 12px;
  color: #606266;
  margin-bottom: 6px;
  font-weight: bold;
}
.ctrl-input {
  padding: 6px 10px;
  border: 1px solid #dcdfe6;
  border-radius: 4px;
  font-size: 12px;
  outline: none;
  background: #fff;
  color: #333;
  transition: all 0.3s;
}
.ctrl-input:focus {
  border-color: #1c3d90;
  box-shadow: 0 0 0 2px rgba(28, 61, 144, 0.1);
}
.modal-footer {
  padding: 12px 20px;
  border-top: 1px solid #ebeef5;
  text-align: right;
  background: #fafafa;
  border-radius: 0 0 6px 6px;
}
</style>