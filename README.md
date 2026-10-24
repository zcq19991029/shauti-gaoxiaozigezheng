# 高校资格证刷题 Sites 版

## 项目定位

“高校资格证刷题 Sites 版”是面向高校教师资格证复习的题库与刷题工具，题目来源标记为 ZCQ。项目服务于需要反复练习教育心理学、教育学题目的教师资格证备考者，也适合在电脑、手机和 ChatGPT Sites 环境中使用。

它解决了三类问题：

1. 将教育心理学、教育学的单选、多选、判断题集中到一个可筛选、可复习的题库中。
2. 用已刷、错题、待掌握、已记住和真题卷资料帮助用户按自己的节奏重复练习。
3. 将用户导入的题库从单一浏览器本地存储扩展为按 ChatGPT 账号隔离的 Sites D1 云端存储，减少本地文件移动、浏览器更换或本地缓存异常造成的影响。

当前正式运行形态是 Cloudflare Worker 承载的 ChatGPT Sites；浏览器本地 IndexedDB/localStorage 仍作为离线和云端不可用时的缓存或回退。

## 项目阶段

以下阶段来自当前仓库的 Git 历史和现有文件，旧阶段的功能不自动代表当前线上版本仍保留全部实现。

1. **静态刷题阶段**：最初是 `index.html`、`app.js`、`styles.css` 和 `data/` 组成的浏览器端刷题网页，使用 GitHub Pages 发布，进度主要保存在浏览器本地。
2. **本地可靠性阶段**：增加本地 HTTP 启动脚本、缓存强制刷新、加载超时和本地题库卡住时的恢复处理，并完善快速复习队列位置和进度显示。
3. **教室离线阶段**：提交过 `课堂版.html` 和 `scripts/build-portable.mjs`，目标是将 CSS、脚本和题库内嵌后在教室电脑上直接打开。该产物仍在仓库中，但当前输入链路和 `file://` 双击兼容性没有重新验证，属于历史/待验证功能。
4. **Sites Worker 阶段**：迁移到 Vinext、React、TypeScript 和 Cloudflare Worker，页面入口改为 `app/page.tsx`，构建产物改为 `dist/server` 与 `dist/client`。
5. **D1 数据阶段**：接入 Drizzle 和 Cloudflare D1，建立按 `user_id + subject` 隔离的 `question_banks` 表，并增加健康检查和题库读写 API。
6. **当前发布阶段**：完成导入题库通过 Worker API 同步到 D1 的代码与构建产物更新，当前 Sites 第 5 版已部署成功。

历史 GitHub Pages 地址曾是 <https://zcq19991029.github.io/shauti-gaoxiaozigezheng/>；当前 README 不把它当作主发布入口，是否仍可访问未在本次核验。

## 当前功能

- 教育心理学、教育学分科目管理；内置题库和教育学真题卷资料分别保存。
- 单选、多选、判断三种题型，支持顺序刷题、随机刷题和按范围筛选。
- “全部待掌握”“错题强化”“尚未作答”“已刷题回看”“已记住回看/移回待掌握”等练习范围。
- 选项作答后的红绿反馈、正确答案、逐项解析、AI 独立复核提示和速记提示。
- 上一题/下一题导航、记住/再练操作、沉浸式刷题、进度条和学习概览。
- 本地学习账号、科目和题型进度；本地浏览器使用 IndexedDB/localStorage 保存刷题状态。
- 导入 PDF、DOCX、TXT、MD、JSON 或粘贴文字；导入前预览，支持题型识别、重复检查和题库导出。
- 已登录 Sites 用户的题库导入同步到当前账号的 D1；不同账号通过 `user_id + subject` 隔离。
- 未登录、离线或云端暂不可用时使用本地缓存回退。
- AI 设置支持 DeepSeek、硅基流动和自定义 OpenAI 兼容接口，可以测试连接、选择模型和生成解析。
- 主题预设包括颜色主题、背景图预设、自定义背景和界面透明度。
- 使用公告、刷题指南、教育学真题卷、更新检查和 AI 提问窗口等辅助入口。

## 技术栈和运行环境

- 前端：React、TypeScript、Vinext、Vite、CSS
- 服务端：Cloudflare Worker / ChatGPT Sites
- 数据库：Cloudflare D1、Drizzle ORM、SQLite 迁移文件
- 资料解析：PDF.js、Mammoth.js，以及浏览器端 TXT/MD/JSON 读取
- 本地运行：Wrangler、Miniflare、Node.js `>=22.13.0`
- 发布配置：Sites `.openai/hosting.json`、Sites 工作流、GitHub 源码仓库

## 目录结构

```text
高校资格证刷题/
├─ app/                         Vinext 页面、布局和 Worker API
│  └─ api/health/               D1 健康检查
│  └─ api/question-banks/       题库 GET/PUT API
├─ components/ui/               可复用 UI 组件
├─ db/                          D1/Drizzle 连接和表结构
├─ drizzle/                     数据库迁移
├─ public/                      浏览器端脚本、样式、题库和解析依赖
│  ├─ data/                     内置题库和真题卷数据
│  └─ vendor/                   PDF.js、Mammoth.js 等依赖
├─ scripts/                     构建、安装、环境和离线版脚本
├─ dist/                        当前提交中的 Worker 构建产物
├─ docs/images/                 项目说明和功能截图
├─ source-materials/            尚未纳入题库的原始资料
├─ .openai/hosting.json         Sites 项目 ID 和 D1 绑定
├─ package.json                 命令和依赖
├─ vite.config.ts               Vinext、Cloudflare 插件和本地 D1 配置
├─ 课堂版.html                  历史离线单文件产物，当前未重新验证
└─ 项目交接.md                  当前工作阶段的继续工作记录
```

## 主要入口和关键模块

| 文件 | 作用 |
| --- | --- |
| `app/page.tsx` | 当前页面主体；以 HTML 片段挂载现有刷题前端结构 |
| `app/layout.tsx`、`app/globals.css` | Vinext 页面布局和全局样式 |
| `app/chatgpt-auth.ts` | 读取 Sites 注入的 ChatGPT 用户身份请求头并处理登录跳转 |
| `app/api/health/route.ts` | 通过 `select 1` 检查 D1 是否可用 |
| `app/api/question-banks/route.ts` | 当前账号按科目读写题库，限制题库大小为 8 MB |
| `db/schema.ts` | `question_banks` 表，复合主键为 `user_id + subject` |
| `db/index.ts` | 从 Cloudflare Worker 环境取得 `DB` 并创建 Drizzle 实例 |
| `public/app.js`、`public/imports.js`、`public/ai.js` | 刷题、资料导入和 AI 交互逻辑 |
| `public/data/` | 教育心理学、教育学和真题卷 JSON 数据 |
| `scripts/run-framework.mjs` | Windows 中文路径下用临时 ASCII junction 运行 Vinext/Vite |
| `scripts/build-verified.sh` | managed-linux 环境下执行有时限的 Vinext 构建 |
| `scripts/build-portable.mjs` | 历史教室单文件生成器，当前需补齐输入链路后再使用 |
| `.openai/hosting.json` | 项目 ID `appgprj_6ab93d9d78148191af68474ba8ab8650` 和逻辑绑定 `DB` |

## 本地启动、构建和测试

安装依赖：

```powershell
npm ci --no-audit --no-fund
```

生成迁移文件：

```powershell
npm run db:generate
```

构建 Worker：

```powershell
npm run build
```

启动本地 Worker：

```powershell
npm start -- --port 8787
```

打开 `http://127.0.0.1:8787/`。本地 API 和 D1 依赖 Worker 环境，直接双击源码 HTML 不代表 Sites 版本的运行方式。

类型检查：

```powershell
npx tsc --noEmit
```

当前 `package.json` 提供 `lint`，但本项目历史记录中没有本次完整 lint 通过记录；运行前应确认忽略目录和依赖安装状态。

## 部署和发布记录

- Sites 项目标题：高校资格证刷题 Sites版。
- 当前线上地址：<https://shauti-gaoxiaozigezheng-sites.zcq991029.chatgpt.site/>。
- 当前线上版本：第 5 版。
- 第 5 版来源提交：`49abd67ca3760d4b21b29ae644797b49a841dc47`。
- 第 5 版部署状态：`succeeded`，部署类型为 production publish。
- 当前 Sites 项目状态：`active`，访问模式为 public。
- D1 绑定：`DB`；已确认的表：`question_banks`。
- 历史 Sites 版本 1 至 4 均保留在 Sites 版本列表中；本次只确认版本 5 的线上部署状态，没有逐一重新验证历史版本。
- GitHub 源码仓库：<https://github.com/zcq19991029/shauti-gaoxiaozigezheng>。

发布时必须保持 `.openai/hosting.json` 的项目 ID 和 `DB` 绑定，使用与当前提交匹配的完整源代码、构建产物和 Sites 工作流；不要只上传单个 HTML 或旧归档。

## 已验证内容

- 已读取并核对当前项目源码、配置、脚本、Git 历史和部署配置。
- Sites 项目为 active，线上第 5 版部署状态为 succeeded，线上地址和项目 ID 匹配。
- D1 `DB` 绑定存在，数据库概览能看到 `question_banks` 表。
- Worker API 的健康检查、账号解析、题库 GET/PUT 路由和 D1 Schema 存在且类型结构通过检查。
- `npx tsc --noEmit` 已通过。
- 构建失败后产生的本地 `dist` 删除标记已从 Git 基线恢复。
- 文档重建前后已运行 `git diff --check`，无空白错误。

## 尚未验证和限制

- 当前源码在 Windows 中文路径下执行一次干净的 `npm run build` 仍未通过；已知失败点是临时 junction 下 `dist` 被占用并返回 `EPERM`。
- 本地 Worker 启动、本地 D1 持久化和 `/api/health` 实际 HTTP 响应未在本轮重新运行。
- 真实登录会话下导入小型 JSON 后的 D1 PUT、GET、刷新恢复和跨账号隔离未完成端到端验证。
- 当前没有读取线上用户题库行，因此无法确认某个具体账号的导入内容已经落库。
- `课堂版.html` 和 `scripts/build-portable.mjs` 的单文件生成、`file://` 双击运行和教室电脑兼容性未重新验证。
- `source-materials/ieee11090147.txt` 与 `source-materials/IET_CP927_2025_目录.pdf` 尚未验证解析质量，也没有自动加入公共题库。
- 旧 GitHub Pages 地址、旧静态版的缓存修复效果和历史离线版在当前环境是否仍可运行，未从当前运行状态确认。

## 外部依赖和边界

- Sites 平台负责 Worker 发布、ChatGPT 登录身份请求头和 D1 绑定；没有 Sites 环境时，云端账号同步不能按线上方式验证。
- GitHub 仓库用于源码版本管理和 Sites 源码同步；用户导入的题库不应提交到 GitHub。
- D1 只保存登录账号自己的题库记录，公共题库修改权限仍需由项目维护者控制。
- DeepSeek、硅基流动或自定义 OpenAI 兼容接口由用户自行提供 Key，费用、模型权限和跨域能力取决于服务商。
- PDF.js、Mammoth.js 等浏览器端依赖由 `public/vendor/` 提供，导入解析不依赖用户移动原始文件后仍可读取已同步数据。

## 后续方向

1. 解决 Windows 中文路径和临时 junction 的构建锁定问题，形成可重复的本地构建检查。
2. 完成登录后导入同步、刷新恢复和跨账号隔离的真实端到端验证。
3. 明确 `source-materials/` 的解析、审核和入库流程，避免未经确认的资料进入公共题库。
4. 如果仍需要教室离线版，修复单文件生成器并单独验证 U 盘/`file://` 场景。
5. 为 Sites 发布补充自动化健康检查、版本提交和 D1 迁移记录，减少线上版本与本地源码不一致。


## 产品专用维护技能

[高校资格证刷题开发与维护](.agents/skills/quiz-pages-release/SKILL.md) 与产品源码一起维护，入口索引见 [skills/README.md](skills/README.md)。本机共享安装目录链接到这份原件，修改说明文档不触发产品发布。


## 统一目录与技能规则核对（2026-10-08）

先读适用父级 AGENTS，再读本项目 README、项目交接和实际任务文件。README 维护长期说明，项目交接维护当前状态；支撑目录不自动创建同名文档。仅独立业务边界才拆子项目；任务证据不当冗余删除。
技能原件只维护一份，产品专用随产品仓库，跨项目新技能默认入提示词系列仓库；已有独立发布源码仓库保留所有权和 Git 更新路径并登记。第三方、系统及插件技能不搬迁。项目/个人入口可按需目录链接，不使用 .lnk；换电脑重新安装或建立链接。区分文件可读、技能发现与实际运行。
本轮仅文档、索引与链接结构核对；没有运行功能测试、部署、修改生产数据或验证硬件。

本项目技能索引：$quiz-pages-release，原件 .agents/skills/quiz-pages-release；个人入口为目录链接。
已存在有价值的历史说明、专项文档和交付包保持原位，不因本轮统一规则覆盖。
