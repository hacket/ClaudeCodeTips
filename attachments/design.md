---
banner: 
date_created: Sunday, July 27th 2025, 11:08:07 am
date_updated: Sunday, July 27th 2025, 10:41:23 pm
title: design
author: hacket
categories: 
category: 
tags: []
toc: true
description: 
dg-publish: true
dg-enable-search: true
dg-show-local-graph: true
dg-show-toc: true
dg-show-file-tree: true
image-auto-upload: true
feed: show
format: list
aliases: [设计文档]
linter-yaml-title-alias: 设计文档
---

# 设计文档

## 概述

" 今天吃什么 " 是一个渐进式 Web 应用，帮助用户解决每日用餐选择困难。系统支持游客模式和注册用户模式，提供菜品管理、智能随机选择、社区分享等功能。应用采用前端静态站点配合 Cloudflare Workers 无服务器后端的架构。

### 核心功能

- 个人菜品/餐厅列表管理
- 基于时间段的随机选择（每餐限制一次）
- 游客账户与注册账户的平滑升级
- 社区分享与发现功能
- 跨设备数据同步

## 架构

### 整体架构

```
[前端 PWA] <--> [Cloudflare Workers API] <--> [Cloudflare Workers KV/Durable Objects]
     |
[本地存储: IndexedDB/localStorage]
```

### 技术栈

- **前端**: 原生 JavaScript + CSS + HTML (PWA)
- **后端**: Cloudflare Workers
- **存储**:
  - 本地: IndexedDB (主要数据) + localStorage (会话信息)
  - 云端: Cloudflare Workers KV (用户数据) + Durable Objects (实时共享)
- **部署**: Cloudflare Pages

### 数据流

1. **游客模式**: 数据仅存储在本地 IndexedDB
2. **注册用户**: 数据同步到 Cloudflare Workers KV，本地缓存提升体验
3. **社区功能**: 使用 Durable Objects 处理实时数据共享

## 组件和接口

### 前端组件架构

#### 1. 核心管理组件

```javascript
// UserManager - 用户身份管理
class UserManager {
  async initializeUser()     // 初始化用户身份
  async upgradeToRegistered() // 游客升级为注册用户
  getCurrentUser()           // 获取当前用户信息
}

// MealManager - 菜品列表管理
class MealManager {
  async addMeal(name)        // 添加菜品
  async removeMeal(id)       // 删除菜品
  async getMeals()           // 获取菜品列表
  async syncWithCloud()      // 云端同步
}

// RandomizerEngine - 随机选择引擎
class RandomizerEngine {
  async randomSelect()       // 执行随机选择
  checkMealTimeRestriction() // 检查时间限制
  getCurrentMealPeriod()     // 获取当前餐次
}
```

#### 2. 用户界面组件

```javascript
// UIController - 主界面控制器
class UIController {
  renderMealList()          // 渲染菜品列表
  showRandomResult()        // 显示随机结果
  showMealTimeRestriction() // 显示时间限制提示
  renderCommunityFeed()     // 渲染社区分享
}

// AnimationManager - 动画效果管理
class AnimationManager {
  playRandomAnimation()     // 随机选择动画
  showSuccessAnimation()    // 成功操作动画
  showErrorAnimation()      // 错误提示动画
}
```

### API 接口设计

#### 1. 用户认证 API

```javascript
// POST /api/auth/register
{
  nickname: string,
  email: string,
  password: string,
  localData?: MealData[] // 游客数据迁移
}

// POST /api/auth/login
{
  email: string,
  password: string
}

// GET /api/auth/profile
Response: UserProfile
```

#### 2. 菜品管理 API

```javascript
// GET /api/meals
Response: MealItem[]

// POST /api/meals
{
  name: string,
  category?: string
}

// DELETE /api/meals/:id
```

#### 3. 随机记录 API

```javascript
// POST /api/random
{
  mealPeriod: 'breakfast' | 'lunch' | 'dinner',
  selectedMeal: string
}

// GET /api/random/status/:period
Response: { canRandomize: boolean, lastRandomTime?: timestamp }
```

#### 4. 社区分享 API

```javascript
// POST /api/community/share
{
  mealName: string,
  mealPeriod: string,
  userNickname: string
}

// GET /api/community/feed/:period
Response: CommunityShare[]
```

## 数据模型

### 1. 用户数据模型

```javascript
// 用户基本信息
interface User {
  id: string;
  nickname: string;
  email?: string;
  isGuest: boolean;
  createdAt: timestamp;
  lastActiveAt: timestamp;
}

// 用户会话信息
interface UserSession {
  userId: string;
  token?: string;
  mealRestrictions: {
    breakfast?: timestamp;
    lunch?: timestamp;
    dinner?: timestamp;
  };
}
```

### 2. 菜品数据模型

```javascript
// 菜品项目
interface MealItem {
  id: string;
  name: string;
  category?: string;
  addedAt: timestamp;
  userId: string;
}

// 用户菜品列表
interface UserMealList {
  userId: string;
  meals: MealItem[];
  lastUpdated: timestamp;
}
```

### 3. 社区分享模型

```javascript
// 分享记录
interface CommunityShare {
  id: string;
  userNickname: string;
  mealName: string;
  mealPeriod: 'breakfast' | 'lunch' | 'dinner';
  sharedAt: timestamp;
  likes?: number;
}

// 分页分享数据
interface CommunityFeed {
  period: string;
  shares: CommunityShare[];
  total: number;
  hasMore: boolean;
}
```

### 4. 本地存储数据结构

```javascript
// IndexedDB 主要数据存储
interface LocalUserData {
  user: User;
  meals: MealItem[];
  randomHistory: RandomRecord[];
  settings: UserSettings;
}

// localStorage 会话数据
interface SessionData {
  currentUserId: string;
  authToken?: string;
  mealRestrictions: MealRestrictions;
  lastSyncTime?: timestamp;
}
```

## 错误处理

### 1. 网络错误处理

```javascript
class NetworkErrorHandler {
  // 离线模式检测和处理
  handleOfflineMode() {
    // 显示离线提示
    // 启用本地模式
    // 队列待同步操作
  }
  
  // API 请求失败重试机制
  async retryRequest(request, maxRetries = 3) {
    // 指数退避重试
    // 失败后降级到本地操作
  }
  
  // 数据同步冲突解决
  resolveDataConflict(localData, remoteData) {
    // 时间戳比较策略
    // 用户选择策略
  }
}
```

### 2. 数据验证错误

```javascript
class ValidationErrorHandler {
  // 用户输入验证
  validateMealName(name) {
    if (!name || name.trim().length === 0) {
      throw new ValidationError('菜品名称不能为空');
    }
    if (name.length > 50) {
      throw new ValidationError('菜品名称过长');
    }
  }
  
  // 邮箱格式验证
  validateEmail(email) {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!emailRegex.test(email)) {
      throw new ValidationError('邮箱格式不正确');
    }
  }
}
```

### 3. 存储错误处理

```javascript
class StorageErrorHandler {
  // 本地存储空间不足
  handleQuotaExceeded() {
    // 清理旧数据
    // 提示用户
    // 降级到内存模式
  }
  
  // IndexedDB 不可用降级
  fallbackToLocalStorage() {
    // 使用 localStorage 作为备选
    // 功能降级提示
  }
}
```

## 时间管理策略

### 1. 餐次时间定义

```javascript
const MEAL_PERIODS = {
  breakfast: { start: 6, end: 10 },   // 6:00-10:00
  lunch: { start: 11, end: 14 },      // 11:00-14:00
  dinner: { start: 17, end: 21 }      // 17:00-21:00
};

class TimeManager {
  getCurrentMealPeriod() {
    const hour = new Date().getHours();
    
    for (const [period, time] of Object.entries(MEAL_PERIODS)) {
      if (hour >= time.start && hour <= time.end) {
        return period;
      }
    }
    
    // 非用餐时间返回最近的下一餐
    return this.getNextMealPeriod(hour);
  }
  
  canRandomizeForPeriod(period, lastRandomTime) {
    if (!lastRandomTime) return true;
    
    const now = new Date();
    const lastRandom = new Date(lastRandomTime);
    
    // 检查是否为同一天的同一餐次
    return !this.isSameDayAndPeriod(now, lastRandom, period);
  }
}
```

### 2. 时区处理

```javascript
class TimezoneManager {
  getUserTimezone() {
    return Intl.DateTimeFormat().resolvedOptions().timeZone;
  }
  
  convertToUserTime(utcTime) {
    return new Date(utcTime).toLocaleString('zh-CN', {
      timeZone: this.getUserTimezone()
    });
  }
}
```

## 测试策略

### 1. 单元测试

```javascript
// 核心逻辑测试
describe('RandomizerEngine', () => {
  test('should return random meal from list', () => {
    const meals = ['麻辣烫', '兰州拉面', '黄焖鸡米饭'];
    const result = randomizer.selectMeal(meals);
    expect(meals).toContain(result);
  });
  
  test('should respect meal time restrictions', () => {
    const lastLunchTime = new Date();
    const canRandomize = randomizer.canRandomizeForPeriod('lunch', lastLunchTime);
    expect(canRandomize).toBe(false);
  });
});

// 时间管理测试
describe('TimeManager', () => {
  test('should identify correct meal period', () => {
    const mockDate = new Date('2023-01-01 12:30:00');
    jest.spyOn(global, 'Date').mockImplementation(() => mockDate);
    
    expect(timeManager.getCurrentMealPeriod()).toBe('lunch');
  });
});
```

### 2. 集成测试

```javascript
// 数据同步测试
describe('Data Sync Integration', () => {
  test('should sync local data to cloud after registration', async () => {
    // 创建游客数据
    await localManager.addMeal('测试菜品');
    
    // 注册账户
    await userManager.register('test@example.com', 'password');
    
    // 验证数据已同步
    const cloudMeals = await apiClient.getMeals();
    expect(cloudMeals).toContainEqual(expect.objectContaining({
      name: '测试菜品'
    }));
  });
});
```

### 3. 端到端测试

```javascript
// 用户流程测试
describe('Complete User Journey', () => {
  test('guest user can upgrade to registered user', async () => {
    // 游客添加菜品
    await page.fill('[data-testid="meal-input"]', '宫保鸡丁');
    await page.click('[data-testid="add-meal"]');
    
    // 随机选择
    await page.click('[data-testid="random-button"]');
    
    // 升级注册
    await page.click('[data-testid="register-button"]');
    await page.fill('[data-testid="email-input"]', 'test@example.com');
    await page.fill('[data-testid="password-input"]', 'password123');
    await page.click('[data-testid="submit-register"]');
    
    // 验证数据迁移成功
    expect(await page.textContent('[data-testid="meal-list"]')).toContain('宫保鸡丁');
  });
});
```

### 4. 性能测试

```javascript
// 大数据量测试
describe('Performance Tests', () => {
  test('should handle large meal lists efficiently', async () => {
    // 添加1000个菜品
    const meals = Array.from({length: 1000}, (_, i) => `菜品${i}`);
    await Promise.all(meals.map(meal => mealManager.addMeal(meal)));
    
    // 测试随机选择性能
    const startTime = performance.now();
    await randomizer.randomSelect();
    const endTime = performance.now();
    
    expect(endTime - startTime).toBeLessThan(100); // 应在100ms内完成
  });
});
```

### 5. 离线功能测试

```javascript
describe('Offline Functionality', () => {
  test('should work offline with local storage', async () => {
    // 模拟离线状态
    await page.setOffline(true);
    
    // 验证基本功能仍可用
    await page.fill('[data-testid="meal-input"]', '离线菜品');
    await page.click('[data-testid="add-meal"]');
    
    expect(await page.textContent('[data-testid="meal-list"]')).toContain('离线菜品');
  });
});
```

## 部署架构

### 1. Cloudflare Pages 部署

```yaml
# wrangler.toml
name = "meal-randomizer"
main = "src/worker.js"
compatibility_date = "2023-01-01"

[env.production]
vars = { ENVIRONMENT = "production" }
kv_namespaces = [
  { binding = "USERS", id = "user-data-kv" },
  { binding = "MEALS", id = "meal-data-kv" }
]

[env.development]
vars = { ENVIRONMENT = "development" }
kv_namespaces = [
  { binding = "USERS", id = "user-data-kv-dev" },
  { binding = "MEALS", id = "meal-data-kv-dev" }
]
```

### 2. 渐进式 Web 应用配置

```json
// manifest.json
{
  "name": "今天吃什么",
  "short_name": "今天吃什么",
  "description": "帮你解决每日用餐选择困难",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#ff6b6b",
  "icons": [
    {
      "src": "/icons/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```

### 3. Service Worker 策略

```javascript
// 缓存策略
const CACHE_NAME = 'meal-randomizer-v1';
const STATIC_ASSETS = [
  '/',
  '/styles.css',
  '/script.js',
  '/manifest.json'
];

// 离线优先策略
self.addEventListener('fetch', event => {
  if (event.request.url.includes('/api/')) {
    // API 请求: 网络优先，离线降级
    event.respondWith(networkFirst(event.request));
  } else {
    // 静态资源: 缓存优先
    event.respondWith(cacheFirst(event.request));
  }
});
```

## 安全考虑

### 1. 认证安全

- 使用 HTTP-Only cookies 存储认证令牌
- 实施 CSRF 保护
- 密码使用 bcrypt 哈希存储
- JWT 令牌设置合理过期时间

### 2. 数据安全

- 用户敏感数据加密存储
- API 请求参数验证和净化
- 防止 XSS 攻击的输入处理
- 实施 Content Security Policy

### 3. 隐私保护

- 最小化数据收集
- 提供数据导出功能
- 支持账户删除
- 遵循数据保护法规

## 性能优化

### 1. 前端优化

- 代码分割和懒加载
- 图片优化和压缩
- CSS 和 JavaScript 压缩
- 使用 Web Workers 处理复杂计算

### 2. 后端优化

- Cloudflare Workers 边缘计算
- KV 存储的读写优化
- API 响应缓存策略
- 数据库查询优化

### 3. 网络优化

- HTTP/2 多路复用
- 资源预加载
- 服务端推送
- CDN 加速

## 监控和分析

### 1. 性能监控

- Core Web Vitals 跟踪
- API 响应时间监控
- 错误率统计
- 用户行为分析

### 2. 业务指标

- 用户注册转化率
- 功能使用频率
- 社区分享参与度
- 用户留存率

这个设计文档基于详细的需求分析和技术研究，提供了完整的架构方案，确保应用既能满足用户需求，又具备良好的可扩展性和维护性。
