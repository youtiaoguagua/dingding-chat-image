# 钉钉聊天截图生成器

## 项目简介
这是一个基于 Vue 3 + Vite + Element Plus 的钉钉风格聊天截图生成器。用户可以自定义聊天背景颜色、气泡颜色、文字颜色、群名称、头像以及聊天内容，并生成高质量的聊天截图。

## 功能特点

### 🎨 颜色自定义
- **背景颜色设置**：支持颜色选择器和十六进制输入
- **气泡颜色设置**：自定义消息气泡背景色
- **文字颜色设置**：调整消息文字颜色

### 👥 群聊设置
- **群名称管理**：自定义群名称并选择是否显示
- **群聊样式**：模拟真实钉钉群聊界面

### 🖼️ 头像管理
- **他人头像**：上传自定义头像图片
- **我的头像**：设置个人头像
- **文字头像**：支持文字头像生成，可自定义背景色和文字颜色
- **一键阿伟**：快速设置默认头像（趣味功能）

### 💬 对话管理
- **消息添加**：支持添加多条聊天消息
- **消息编辑**：修改已存在的聊天内容
- **消息排序**：通过上下箭头调整消息顺序
- **消息删除**：删除不需要的聊天记录
- **发送者设置**：区分"我"和"他人"的消息

### 📸 截图功能
- **实时预览**：所见即所得的聊天效果预览
- **高质量截图**：基于 html2canvas 生成清晰截图
- **一键下载**：生成截图后自动下载

## 技术栈
- **Vue 3**：现代化的渐进式 JavaScript 框架
- **Vite**：快速的前端构建工具
- **Element Plus**：基于 Vue 3 的组件库
- **html2canvas**：用于生成 DOM 截图
- **SCSS**：CSS 预处理器，提供更好的样式组织

## 项目结构
```
dingding-chat-image/
├── index.html                 # 主 HTML 文件
├── package.json              # 项目配置和依赖管理
├── pnpm-lock.yaml           # pnpm 锁定文件
├── vite.config.js           # Vite 配置文件
├── jsconfig.json            # JavaScript 配置
├── README.md                # 项目说明文档
├── public/                  # 静态资源目录
│   └── favicon.png         # 网站图标
└── src/                    # 源代码目录
    ├── App.vue             # 主应用组件
    ├── main.js             # 应用入口文件
    └── assets/             # 资源文件
        └── main.scss       # 主样式文件
```

## 环境要求
- **Node.js** >= 16.0.0
- **pnpm** >= 7.0.0 （推荐）或 **npm** >= 8.0.0

## 安装与运行

### 1. 克隆项目
```bash
git clone <项目地址>
cd dingding-chat-image
```

### 2. 安装依赖
```bash
# 使用 pnpm（推荐）
pnpm install

# 或使用 npm
npm install
```

### 3. 启动开发服务器
```bash
# 使用 pnpm
pnpm run dev

# 或使用 npm
npm run dev
```

### 4. 构建生产版本
```bash
# 使用 pnpm
pnpm run build

# 或使用 npm
npm run build
```

### 5. 预览构建结果
```bash
# 使用 pnpm
pnpm run preview

# 或使用 npm
npm run preview
```

## 使用说明

### 基本操作流程
1. **启动应用**：访问 `http://localhost:5173`（默认端口）
2. **颜色设置**：在"颜色设置"面板中调整背景色、气泡色和文字色
3. **群名称**：设置群聊名称并选择是否显示
4. **头像配置**：上传或设置聊天参与者的头像
5. **添加消息**：在"对话管理"中添加聊天内容
6. **实时预览**：右侧面板实时显示聊天效果
7. **生成截图**：点击"下载截图"保存聊天图片

### 高级功能
- **消息排序**：使用上下箭头调整消息顺序
- **批量编辑**：支持选中消息进行批量操作
- **样式同步**：颜色设置支持选择器和手动输入双向同步
- **响应式设计**：支持不同屏幕尺寸的良好显示

## 主要依赖

### 生产依赖
- `vue@^3.5.17` - Vue.js 核心框架
- `element-plus@^2.10.2` - UI 组件库
- `@element-plus/icons-vue@^2.3.1` - Element Plus 图标
- `html2canvas@^1.4.1` - DOM 截图库

### 开发依赖
- `@vitejs/plugin-vue@^6.0.0` - Vue 插件支持
- `vite@^7.0.0` - 构建工具
- `sass-embedded@^1.89.2` - SCSS 编译器
- `vite-plugin-vue-devtools@^7.7.7` - Vue 开发工具

## 开发指南

### 代码结构
- 主要逻辑集中在 `src/App.vue` 中
- 使用 Vue 3 Composition API
- 响应式数据管理和事件处理
- 模块化的样式组织

### 自定义样式
项目使用 SCSS 进行样式管理，主要样式文件位于：
- `src/assets/main.scss` - 全局样式
- `src/App.vue` 中的 `<style>` 部分 - 组件样式

## 贡献指南
1. Fork 本项目
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

## 许可证
本项目基于 MIT 许可证开源 - 查看 [LICENSE](LICENSE) 文件了解详情

## 更新日志
- **v0.0.0** - 初始版本
  - 基础聊天截图生成功能
  - 颜色自定义
  - 头像管理
  - 消息管理
  - 截图下载

---

如有问题或建议，欢迎提交 [Issue](../../issues) 或 [Pull Request](../../pulls)！
