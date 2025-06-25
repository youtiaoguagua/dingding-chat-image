# Dingding Chat Image Generator

## 项目简介
Dingding Chat Image Generator 是一个基于 Vue 3 和 Element Plus 的聊天截图生成器。用户可以自定义聊天背景颜色、气泡颜色、文字颜色、群名称、头像以及聊天内容，并生成聊天截图。

## 功能特点
- **颜色设置**：支持自定义聊天背景颜色、气泡颜色和文字颜色。
- **群名称设置**：可设置群名称并选择是否显示。
- **头像设置**：支持上传自定义头像、使用默认头像或文字头像。
- **对话管理**：添加、编辑、删除和排序聊天消息。
- **截图生成**：生成聊天内容的截图并下载。
- **一键阿伟**：快速设置阿伟头像。

## 技术栈
- **Vue 3**：用于构建用户界面。
- **Element Plus**：提供丰富的 UI 组件。
- **html2canvas**：用于生成聊天截图。
- **SCSS**：用于样式设计。

## 项目结构
```
├── index.html
├── jsconfig.json
├── main.html
├── package.json
├── pnpm-lock.yaml
├── README.md
├── vite.config.js
├── public
│   ├── favicon.ico
│   └── favicon.png
├── src
│   ├── App.vue
│   ├── main.js
│   └── assets
│       └── main.scss
```

## 安装与运行

### 环境要求
- Node.js >= 16
- pnpm >= 7

### 安装依赖
```bash
pnpm install
```

### 启动项目
```bash
pnpm run dev
```

### 构建项目
```bash
pnpm run build
```

## 使用说明
1. 启动项目后，打开浏览器访问 `http://localhost:3000`。
2. 在控制面板中设置聊天背景颜色、气泡颜色、文字颜色等。
3. 添加或编辑聊天消息。
4. 点击“更新预览”查看效果。
5. 点击“下载截图”生成聊天截图。

## 贡献
欢迎提交 issue 或 pull request 来改进此项目。

## 许可证
本项目基于 MIT 许可证开源。
