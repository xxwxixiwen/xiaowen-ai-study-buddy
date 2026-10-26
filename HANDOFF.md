# HANDOFF · 小文 AI 学伴（基于 DeepTutor 二开）

需求原文：`K12_REPLICA_BRIEF.md`。内容素材：`k12-content/`（原创，勿重写）。

## 环境与启动
- Python 3.14 `.venv`，已切 **可编辑安装**：`.venv/bin/pip install -e ".[server]"`
- 前端：`cd web && npm ci --legacy-peer-deps`（Node 24 可用，官方推荐 22）
- 启动（开发）：`.venv/bin/deeptutor start --dev --no-browser` → 前端 http://localhost:3782 ，后端 http://127.0.0.1:8001
- 注意：沙箱里运行 CLI 会因写 `data/user/logs` 报 PermissionError，需在完整权限下运行。
- 界面语言：`data/user/settings/interface.json` → `language: zh`
- 截图：`cd web && npx playwright screenshot --channel=chrome --viewport-size=1440,900 --wait-for-timeout=6000 http://localhost:3782/chat out.png`

## 模型（Key 由用户在网页 设置 → 提供方 / 语言模型 / 嵌入模型 填写，禁止入库）
- LLM：DeepSeek `https://api.deepseek.com/v1`，`deepseek-chat` / `deepseek-reasoner`
- Embedding：硅基流动 `https://api.siliconflow.cn/v1`，`BAAI/bge-m3`，1024 维

## 任务进度
- [~] 1 环境：可编辑安装 ✅ 前端依赖 ✅ dev 启动 ✅ 中文 ✅；模型待用户填 Key → `deeptutor doctor --online --format json`
- [x] 2 白牌：完成（typecheck / i18n:check / build 通过，lint 无 error）
- [ ] 3 小文 Persona/Skill + 知识库（完成后停下等确认）
- [ ] 4 视频字幕问答（只给缺口插件方案）
- [ ] 5 收尾 README / .gitignore / 合规检索 / push 命令

## 白牌改动清单（只动视觉/品牌层）
- 品牌资源：`web/public/brand/`（logo-mark.svg、avatar-xiaowen.svg、favicon 16/32、apple-touch-icon）
- 品牌常量：`web/lib/brand.ts`；品牌组件：`web/components/brand/Brand.tsx`
- 主题：`web/app/brand.css`（覆盖默认 snow 主题为暖阳珊瑚色 + 薄荷/阳光辅色、头像/气泡/加载点/登录背景/页脚）
- `web/app/layout.tsx`（标题、favicon、Nunito 圆体、lang=zh-CN）、`web/tailwind.config.js`（标题字体换圆润中文栈）
- Logo/头像替换：`SidebarShell.tsx`、`AppShell.tsx`、`ChatWorkspace.tsx`（欢迎区）、`SessionLoadingView.tsx`（加载态）、`(auth)/login|register`、`(auth)/layout.tsx`
- 文案：`web/locales/*/app.json|common.json` 取值中 DeepTutor→小文/Xiaowen，新增 `brand.*` 键，欢迎语改学伴口吻
- 去残留：`TracePresentation.tsx` 默认名、`AboutSettingsSection.tsx` 移除上游项目链接、`settings-nav.ts`、`SubagentSettingsEditor.tsx`、co-writer 示例模板、avatar-preview
- 删除 `web/public/` 下 DeepTutor logo/banner/favicon
- 后端默认 `sidebar_description`：`deeptutor/api/routers/settings.py`、`deeptutor/services/setup/init.py`
- 一键换皮：替换 `web/public/brand/*` + 改 `web/app/brand.css` 顶部 `--brand-*` + `locales` 的 `brand.*`

## 踩坑
- sharp 渲染 SVG：文件需纯 ASCII（中文用 `&#x...;` 实体），否则 XML 编码报错。
- i18n:audit 对 de/pl 严格：新增键要补到全部语种。
- 内置浏览器截图超时，改用 Playwright + 系统 Chrome。

## 待办 / 下一步
- 用户填 Key → doctor 全绿 → 任务 3。
- 后端提示词仍自称 DeepTutor（`deeptutor/agents/chat/prompts/*/chat_agent.yaml`），任务 3 用 Persona 覆盖并评估是否改提示词。
