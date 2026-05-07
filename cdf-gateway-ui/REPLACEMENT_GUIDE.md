# CDF AI Gateway Web UI 替换文档

## 📋 文档概述

本文档用于指导开发团队将设计稿中的占位内容替换为实际生产环境中的真实数据、品牌资源和配置信息。

---

## 🎨 一、品牌资源替换

### 1.1 Logo 替换

**当前状态**: 使用 CSS 绘制的占位 Logo  
**替换位置**: `index.html` 第 45-50 行（导航栏）和第 180-190 行（首页中央）

```html
<!-- 原代码 -->
<div class="logo-placeholder">CDF</div>

<!-- 替换为 -->
<img src="./assets/logo.png" alt="CDF Logo" class="brand-logo">
```

**要求**:
- Logo 文件格式：SVG（推荐）或 PNG（透明背景）
- 尺寸建议：200x60px（导航栏），400x120px（首页）
- 存放路径：`/assets/logo.png` 或 `/assets/logo.svg`

### 1.2 品牌色调整

**当前主色**: `#2563EB` (蓝色)  
**配置文件**: `index.html` 第 15-25 行（CSS 变量定义）

```css
:root {
  --primary-color: #2563EB;      /* 替换为实际品牌蓝 */
  --primary-light: #3B82F6;      /* 浅蓝色 */
  --primary-dark: #1D4ED8;       /* 深蓝色 */
  --accent-color: #06B6D4;       /* 辅助青色 */
  --gradient-start: #2563EB;     /* 渐变起始色 */
  --gradient-end: #06B6D4;       /* 渐变结束色 */
}
```

**操作**: 根据品牌 VI 手册调整上述颜色值。

---

## 📊 二、数据接口替换

### 2.1 Dashboard 统计数据

**当前状态**: 静态模拟数据  
**替换位置**: `index.html` 第 350-450 行（JavaScript 数据对象）

```javascript
// 原代码
const dashboardData = {
  totalCalls: 128547,
  successRate: 99.2,
  activeUsers: 342,
  avgResponseTime: 125
};

// 替换为 API 调用
async function fetchDashboardData() {
  const response = await fetch('/api/v1/dashboard/stats');
  return await response.json();
}
```

**API 端点**:
| 指标 | API 端点 | 请求方法 |
|------|---------|---------|
| 总调用量 | `/api/v1/dashboard/stats` | GET |
| 成功率 | `/api/v1/dashboard/success-rate` | GET |
| 活跃用户 | `/api/v1/dashboard/active-users` | GET |
| 平均响应时间 | `/api/v1/dashboard/response-time` | GET |

### 2.2 图表数据替换

**当前状态**: Chart.js 模拟数据  
**替换位置**: `index.html` 第 500-600 行（Chart 配置）

```javascript
// 原代码
const callTrendChart = new Chart(ctx, {
  data: {
    labels: ['00:00', '04:00', '08:00', ...],
    datasets: [{
      label: '调用量',
      data: [120, 85, 340, ...]  // 替换为动态数据
    }]
  }
});

// 替换为
async function loadCallTrendData() {
  const response = await fetch('/api/v1/analytics/calls?period=24h');
  const data = await response.json();
  callTrendChart.data.labels = data.timestamps;
  callTrendChart.data.datasets[0].data = data.values;
  callTrendChart.update();
}
```

**API 端点**:
- 调用趋势：`/api/v1/analytics/calls?period={24h|7d|30d}`
- 成功率趋势：`/api/v1/analytics/success-rate?period={24h|7d|30d}`
- API 分布：`/api/v1/analytics/api-distribution`

### 2.3 API 列表数据

**当前状态**: 硬编码数组  
**替换位置**: `index.html` 第 700-850 行

```javascript
// 原代码
const apiList = [
  { id: 1, name: '文本生成', endpoint: '/v1/completions', ... },
  { id: 2, name: '图像识别', endpoint: '/v1/vision', ... }
];

// 替换为
async function fetchApiList(search = '', sort = 'name') {
  const params = new URLSearchParams({ search, sort });
  const response = await fetch(`/api/v1/apis?${params}`);
  return await response.json();
}
```

**API 端点**: `/api/v1/apis?search={keyword}&sort={name|calls|status}&page={n}&limit={n}`

### 2.4 API 调用功能

**当前状态**: 模拟调用结果  
**替换位置**: `index.html` 第 950-1100 行（调用函数）

```javascript
// 原代码
async function callApi(endpoint, params) {
  // 模拟延迟和返回
  await new Promise(r => setTimeout(r, 800));
  return { success: true, data: {...} };
}

// 替换为
async function callApi(endpoint, params) {
  const response = await fetch(`/api/v1/proxy${endpoint}`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${getUserToken()}`
    },
    body: JSON.stringify(params)
  });
  
  if (!response.ok) {
    throw new Error(`API Error: ${response.status}`);
  }
  
  return await response.json();
}
```

### 2.5 历史记录数据

**当前状态**: 本地模拟数组  
**替换位置**: `index.html` 第 1150-1250 行

```javascript
// 替换为
async function fetchCallHistory(apiId, page = 1, limit = 10) {
  const response = await fetch(
    `/api/v1/history?api_id=${apiId}&page=${page}&limit=${limit}`
  );
  return await response.json();
}
```

**API 端点**: `/api/v1/history?api_id={id}&page={n}&limit={n}`

---

## 👥 三、用户与权限替换

### 3.1 用户信息

**当前状态**: 静态用户信息  
**替换位置**: `index.html` 第 60-80 行（导航栏用户区）

```javascript
// 原代码
const currentUser = {
  name: '张三',
  email: 'zhangsan@cdf.com',
  avatar: null,
  role: 'admin'
};

// 替换为
async function fetchCurrentUser() {
  const response = await fetch('/api/v1/user/profile');
  return await response.json();
}
```

**API 端点**: `/api/v1/user/profile`

### 3.2 用户管理列表

**当前状态**: 硬编码用户表格  
**替换位置**: `index.html` 第 1400-1550 行（设置页）

```javascript
// 替换为
async function fetchUserList(page = 1, filters = {}) {
  const params = new URLSearchParams({
    page,
    ...filters
  });
  const response = await fetch(`/api/v1/admin/users?${params}`);
  return await response.json();
}
```

**API 端点**: `/api/v1/admin/users?page={n}&role={role}&status={status}`

### 3.3 权限配置

**当前状态**: 静态权限选项  
**替换位置**: `index.html` 第 1600-1700 行

```javascript
// 替换为
async function fetchPermissions() {
  const response = await fetch('/api/v1/admin/permissions');
  return await response.json();
}

async function updateUserPermissions(userId, permissions) {
  const response = await fetch(`/api/v1/admin/users/${userId}/permissions`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ permissions })
  });
  return await response.json();
}
```

---

## ⚙️ 四、系统配置替换

### 4.1 系统配置项

**当前状态**: 静态表单  
**替换位置**: `index.html` 第 1750-1850 行

```javascript
// 加载配置
async function fetchSystemConfig() {
  const response = await fetch('/api/v1/admin/config');
  return await response.json();
}

// 保存配置
async function saveSystemConfig(config) {
  const response = await fetch('/api/v1/admin/config', {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(config)
  });
  return await response.json();
}
```

**配置项 API**:
- 获取：`GET /api/v1/admin/config`
- 更新：`PUT /api/v1/admin/config`
- 重置：`POST /api/v1/admin/config/reset`

---

## 🔐 五、认证与安全

### 5.1 登录认证

**需新增功能**: 登录页面和认证流程

```javascript
// 新增登录函数
async function login(email, password) {
  const response = await fetch('/api/v1/auth/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password })
  });
  
  const data = await response.json();
  if (data.token) {
    localStorage.setItem('auth_token', data.token);
    localStorage.setItem('token_expiry', data.expires_at);
  }
  return data;
}

// Token 验证中间件
function getUserToken() {
  const token = localStorage.getItem('auth_token');
  const expiry = localStorage.getItem('token_expiry');
  
  if (!token || Date.now() > new Date(expiry).getTime()) {
    redirectToLogin();
    return null;
  }
  
  return token;
}
```

### 5.2 请求拦截器

```javascript
// 添加全局请求拦截
const originalFetch = window.fetch;
window.fetch = async function(url, options = {}) {
  // 跳过登录接口
  if (url.includes('/auth/')) {
    return originalFetch(url, options);
  }
  
  const token = getUserToken();
  if (!token) {
    redirectToLogin();
    return Promise.reject(new Error('Unauthorized'));
  }
  
  options.headers = {
    ...options.headers,
    'Authorization': `Bearer ${token}`
  };
  
  return originalFetch(url, options);
};
```

---

## 🌐 六、环境配置

### 6.1 API 基础地址

**当前状态**: 相对路径  
**建议配置**: 根据环境切换

```javascript
// 新增配置文件：config.js
const API_CONFIG = {
  development: 'http://localhost:8080/api/v1',
  staging: 'https://staging-api.cdf.com/api/v1',
  production: 'https://api.cdf.com/api/v1'
};

const getApiBaseUrl = () => {
  const env = process.env.NODE_ENV || 'development';
  return API_CONFIG[env];
};
```

### 6.2 功能开关

```javascript
// 功能标志配置
const FEATURE_FLAGS = {
  enableNewDashboard: true,
  enableAdvancedAnalytics: false,
  enableBetaFeatures: false,
  maxUploadSize: 10 * 1024 * 1024, // 10MB
  sessionTimeout: 30 * 60 * 1000    // 30分钟
};
```

---

## 📱 七、响应式适配检查

### 7.1 断点验证

确保以下断点正常工作：

| 设备类型 | 宽度范围 | 测试重点 |
|---------|---------|---------|
| 移动端 | < 768px | 导航折叠、卡片堆叠 |
| 平板端 | 768px - 1024px | 双列布局、侧边栏 |
| 桌面端 | > 1024px | 完整布局、多列展示 |

### 7.2 触摸优化

- [ ] 按钮点击区域 ≥ 44x44px
- [ ] 滑动操作支持（图表、列表）
- [ ] 长按菜单（移动端）

---

## 🧪 八、测试清单

### 8.1 功能测试

- [ ] Dashboard 数据加载正常
- [ ] API 列表搜索和排序正确
- [ ] API 调用成功/失败处理
- [ ] 历史记录分页加载
- [ ] 用户管理增删改查
- [ ] 权限配置生效
- [ ] 系统配置保存

### 8.2 兼容性测试

- [ ] Chrome (最新)
- [ ] Firefox (最新)
- [ ] Safari (最新)
- [ ] Edge (最新)
- [ ] 移动端 Safari
- [ ] 移动端 Chrome

### 8.3 性能测试

- [ ] 首屏加载时间 < 2s
- [ ] 图表渲染时间 < 500ms
- [ ] 列表滚动流畅（60fps）
- [ ] 内存占用合理

---

## 📝 九、部署步骤

### 9.1 构建准备

```bash
# 1. 安装依赖
npm install

# 2. 配置环境变量
cp .env.example .env
# 编辑 .env 文件，填入实际配置

# 3. 替换品牌资源
# 将 Logo 文件放入 /public/assets/ 目录

# 4. 构建生产版本
npm run build
```

### 9.2 部署配置

**Nginx 示例配置**:

```nginx
server {
    listen 80;
    server_name gateway.cdf.com;
    
    root /var/www/cdf-gateway;
    index index.html;
    
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    location /api/ {
        proxy_pass http://backend-server:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

---

## 🆘 十、常见问题

### Q1: Logo 显示模糊？
**解决**: 使用 SVG 格式或提供 2x/3x 倍率的 PNG 资源。

### Q2: 图表数据不更新？
**解决**: 检查 API 返回格式是否与前端期望一致，确认 CORS 配置。

### Q3: 移动端布局错乱？
**解决**: 检查 viewport meta 标签，确认媒体查询断点正确。

### Q4: 认证失败？
**解决**: 确认 Token 存储方式，检查过期时间逻辑，验证 API 端点认证中间件。

---

## 📞 十一、联系方式

如有问题，请联系：

- **前端负责人**: [姓名] - [邮箱]
- **后端负责人**: [姓名] - [邮箱]
- **设计师**: [姓名] - [邮箱]
- **项目管理**: [姓名] - [邮箱]

---

**文档版本**: v1.0  
**最后更新**: 2025-01-15  
**维护团队**: CDF AI Gateway 开发组
