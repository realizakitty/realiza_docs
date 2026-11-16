# Realiza

响应式愿望平台。前台采用 React、TypeScript、Vite、Tailwind CSS；后台采用 React、Ant Design；API 使用 Go、Gin、MongoDB、AWS SES 和 S3 兼容存储。

## 本地预览

前台（终端一）：

```powershell
cd web
npm ci
npm run dev
```

访问 http://127.0.0.1:5173 。未接通服务时，页面显示明确标注的示例愿望以及连接失败提示；示例不允许提交帮助意向。真实表单只在 API 成功后显示成功信息。

管理后台（终端二）：

```powershell
cd admin
npm ci
npm run dev
```

访问 http://127.0.0.1:5174 。

API（终端三）：按 `server/.env.example` 设置环境变量（不会自动读取 .env），并在 `server` 执行 `go run .`。MongoDB 必须可连接；本地可显式使用 `MAIL_MODE=console`，在私有服务日志获取验证链接。正式发送需要独立的 SES 身份及凭据。上传照片需要配置 S3 兼容存储。

开发时 PUBLIC_ORIGIN 使用 http://127.0.0.1:5173，ADMIN_ORIGIN 使用 http://127.0.0.1:5174，注意 localhost 与 127.0.0.1 不等价。

## 实现范围

- PC 多列、手机单列愿望墙；详情、发布、帮助意向、举报、流程说明。
- 简体中文、繁体中文、英语、巴西葡萄牙语、西班牙语；首次按浏览器系统语言匹配，记忆手动选择，可恢复跟随系统。未支持语言回退 pt-BR。
- 用户愿望保留原文，不自动翻译。邮箱不公开；现支持注册及个人记录，保留匿名浏览和发布。
- 邮箱验证后进入审核，送礼意向不会自动改变履约状态。
- 管理后台登录、审核/下架、状态管理、帮助意向跟进、举报处理、运营计数。
- 图片上传与服务端校验；X 分享使用 Go `/share/:id` 输出预览信息。

## 检查

```powershell
cd web
npm run build
npm test
cd ../admin
npm run build
cd ../server
go test ./...
```

## 部署

已提供 GitHub Action：`.github/workflows/deploy.yml`。沿用 P2L 的 `release-prod`、HOST/PORT 和 SSH 凭据约定，统一发布前台、后台和 Go 服务；首次配置步骤见 [deploy/README.md](deploy/README.md)。

构建 web 和 admin，将两个 dist 分别作为前台、后台静态站点。API 为独立 Go 进程。前台同域 `/api/*`、`/share/*` 转发到 Go，其余路由回退前台 index.html；后台同域 `/api/*` 转发到 Go，其余路由回退后台 index.html。保持两个站点同一主域；服务端配置对应来源及 HTTPS Cookie。

未自动部署。按用户授权，本地运行读取 P2L 的 SES/COS 配置；不复用 P2L 数据库或管理员密码。当前隐私/条款页面为产品说明初稿；感谢内容自助提交、邮箱管理链接、独立删除申请流程仍待完善（目前通过举报联系管理员下架）。

## 已接通的本地环境

- 前台：http://127.0.0.1:4173 ，后台：http://127.0.0.1:5174 ，API：http://127.0.0.1:8080 。
- MongoDB：本机 27017，独立 `realiza` 数据库。
- 邮件：沿用 P2L SES 配置，发件人为用户指定的 `p2l@play2launch.com`。
- 图片：沿用 P2L COS，所有新对象隔离到 `realiza/wishes/`。
- 启动 API：在 server 执行 `./start-local.ps1`。仅导入 P2L 邮件及图片设置，不修改 P2L 配置。
- 服务会话密钥保存在 `server/.local.json`（已忽略版本控制，限制为 Administrator/SYSTEM 访问）。此前的共享管理员密码已停用，改为用户账号和角色授权。
- 真实联调已通过：MongoDB、SES 官方模拟收件地址、验证接口、审核/状态/举报、COS 上传与 CDN 读取。模拟收件地址没有真实收件箱，普通邮箱收信仍需实际体验确认。

`assets/desktop-reference.png` 保存已确认视觉稿。新建目录已显式授予 Administrator 可继承完全控制权限。

## 品牌与桌面图标

品牌图标现已统一为猫咪图标，已替换前台页头/页脚、登录页、前后台 favicon 和文档首页；对外资源使用 cat 前缀文件名以更新缓存。前台提供 16/32px PNG、多尺寸 ICO、180px Apple touch icon，以及 192/512px 普通图标和 512px maskable 图标；`site.webmanifest` 设置桌面名称 Realiza、独立窗口模式和品牌主题色。`assets/export-icons.py` 可重新导出尺寸。桌面图标资源已检查，真实 iOS/Android 添加体验仍需真机确认；手机访问正式网站需 HTTPS，手机 localhost 指向手机自身。当前不提供离线缓存。

## 用户系统与超级管理员

前台 `/register` 注册，使用邮件中的六位验证码确认后登录。`/login`、`/forgot-password`、`/reset-password` 提供登录与密码恢复；`/account` 显示本人用户 ID、愿望和帮助意向。登录用户创建的记录绑定用户 ID，历史匿名记录不会仅因邮箱相同自动归入账号。

后台使用同一套已验证的用户账号。超管指定遵循 P2L 的 `admin.user_ids` 规则，但只接受 Realiza 本站用户 ID，不读取 P2L 管理员配置：

1. 注册并验证 Realiza 账号，在“我的账号”复制用户 ID。
2. 将 `server/config.example.yml` 复制为 `server/config.local.yml`，填写 `admin.user_ids` 列表。
3. 重新运行 `server/start-local.ps1`。也可使用 `REALIZA_ADMIN_USER_IDS` 环境变量指定逗号分隔的用户 ID，其值覆盖文件配置。
4. 使用同一邮箱/密码进入后台，配置超管可以为其他已验证账号授予内容审核、送礼协调或数据只读角色，并停用后台访问。授权变更需要原因和当前版本，超管身份不能在后台页面授予。

超管列表默认为空，没有首个用户自动提权。旧单密码与旧管理员 Cookie 均不能登录新版后台。会话通过 HttpOnly Cookie 保存，退出立即撤销当前会话，密码重置撤销原有会话；后台权限由服务端逐次检查。
