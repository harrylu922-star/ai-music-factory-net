# aimusicfactory.net 占位页：部署与养站指南

按下面顺序完成：先改 DNS，再选托管并部署，最后对照「隔离策略」检查。

---

## 第一步：把 DNS 指向 Cloudflare

目标：域名用 Cloudflare 的 Nameservers，获得隐藏 IP、全球加速和免费 HTTPS。

### 1.1 在 Cloudflare 添加站点

1. 打开 [Cloudflare 官网](https://www.cloudflare.com)，登录或注册。
2. 点击 **Add a site**，输入 `aimusicfactory.net`（若有多个域名，逐个添加）。
3. 选择 **Free** 计划，继续。
4. Cloudflare 会扫描该域名的现有 DNS 记录；确认后点击 **Continue**。

### 1.2 修改域名的 Nameservers

1. 在「Overview」页，Cloudflare 会给出两条 **Nameserver** 地址，形如：
   - `xxx.ns.cloudflare.com`
   - `yyy.ns.cloudflare.com`
2. 到 **域名注册商**（买域名的平台，如 GoDaddy、Namecheap、阿里云、腾讯云等）：
   - 找到「DNS 管理」或「Nameservers / 域名服务器」。
   - 把原来的 Nameservers **改成** Cloudflare 提供的两条（并删除或替换掉旧的）。
   - 保存。
3. 回到 Cloudflare，点击 **Done, check nameservers**。生效通常需几分钟到 48 小时，多数在 1 小时内。

### 1.3 确认与 SSL

- 在 Cloudflare 控制台：**SSL/TLS** → 加密模式选 **Full** 或 **Full (strict)**（若托管平台提供证书则用 Full (strict)）。
- 生效后，访问 `https://aimusicfactory.net` 应能打开（内容要等第二步部署才有）。

**第一步完成标志**：域名已使用 Cloudflare 的 Nameservers，且 SSL 为「已加密」。

---

## 第二步：选托管平台并部署静态页

目标：不买服务器，用免费静态托管把本仓库的 `index.html` 部署上去，并让域名指向该托管。

任选其一即可：**Vercel** 或 **GitHub Pages**（也可用 Netlify / Cloudflare Pages，流程类似）。

### 方案 A：Vercel 部署

1. **准备代码**  
   在 Cursor 里确保当前项目有 `index.html` 和（可选）`robots.txt`，然后推送到 **GitHub** 新仓库（若尚未推送）：
   - 本地：`git init` → `git add .` → `git commit -m "Placeholder page"` → `git remote add origin <你的仓库URL>` → `git push -u origin main`。

2. **在 Vercel 导入项目**  
   - 打开 [vercel.com](https://vercel.com)，用 GitHub 登录。  
   - **Add New** → **Project**，从列表选择该仓库（或 Import 填仓库 URL）。  
   - Framework Preset 选 **Other** 或 **Vite** 无所谓，根目录保持仓库根目录即可（本仓库根目录即有 `index.html`）。  
   - 点击 **Deploy**。

3. **绑定自定义域名**  
   - 部署完成后，进入项目 → **Settings** → **Domains**。  
   - 添加 `aimusicfactory.net` 和（若需要）`www.aimusicfactory.net`。  
   - 按页面提示在 **Cloudflare** 里添加/检查 CNAME 或 A 记录（Vercel 会给出目标地址）。  
   - 若 DNS 已在 Cloudflare，在 Cloudflare 的 **DNS** 里添加一条 **CNAME**：  
     - 名称：`@` 或 `www`（按 Vercel 要求），目标：`cname.vercel-dns.com`（以 Vercel 实际显示为准）。  
   - 保存后等 SSL 生效，用浏览器访问 `https://aimusicfactory.net` 检查。

### 方案 B：GitHub Pages 部署

1. **推送代码到 GitHub**  
   同上，确保 `index.html`（及可选 `robots.txt`）在仓库根目录并已 push。

2. **开启 GitHub Pages**  
   - 仓库 → **Settings** → **Pages**。  
   - Source 选 **Deploy from a branch**。  
   - Branch 选 `main`（或你的默认分支），目录选 **/ (root)**。  
   - Save。几分钟后访问 `https://<你的用户名>.github.io/<仓库名>/` 应能看到占位页。

3. **绑定自定义域名**  
   - 同一 **Pages** 设置页，在 **Custom domain** 填 `aimusicfactory.net`，Save。  
   - 在 **Cloudflare** → **DNS** 添加记录：  
     - 类型 **CNAME**，名称 `@`，目标 `<你的用户名>.github.io`（GitHub 文档会写清）；或按 GitHub 提示用 A 记录。  
   - 在 GitHub Pages 设置里勾选 **Enforce HTTPS**（等 DNS 生效后再勾选通常更容易成功）。

**第二步完成标志**：访问 `https://aimusicfactory.net` 能看到本仓库的占位说明页，且为 HTTPS。

---

## 第三步：部署时的「隔离」策略（SEO 养号）

为防止 Google 把多个占位域名视为「克隆站」导致降权，请严格做到下面三点。

| 要素 | 错误做法（易降权） | 正确做法（养号） |
|------|---------------------|------------------|
| **IP 地址** | 多个域名都解析到同一台服务器 IP | 使用 Cloudflare 后，流量走 CDN 节点，天然分散，无需改。 |
| **HTML / 文案** | 多个站 1:1 复制同一套占位代码和文案 | **每个域名一套原创文案**：标题、描述、段落、词频都要不同，连「Coming Soon」的表述也要区分。 |
| **GA / GSC** | 多个域名共用一个 Google Analytics / Search Console 配置 | **测试阶段每个域名独立**：各自单独的 GA 属性、单独的 GSC 站点，不共用同一 ID。 |

### 本仓库（aimusicfactory.net）已满足的隔离项

- **文案**：当前 `index.html` 为 aimusicfactory.net 单独撰写，从「AI + Music + Factory」解析业务与特色，未与其他站共用同一套文字。
- **IP**：你使用 Cloudflare 后，由 CDN 分配节点，符合「分散」要求。
- **GA/GSC**：本仓库未嵌入任何 GA 或 GSC 代码；若你后续加统计，请仅为 aimusicfactory.net 单独建属性/站点，不要与其他域名共用同一 ID。

### 若你还有其他占位域名

- 每个域名单独一个仓库或单独分支，**各自一份不同的 index.html**（不同 title、description、正文、站点名）。
- 每个域名在 Google Search Console 中「添加资源」时单独添加，不要用一个 GSC 账号把多个域名都绑到同一个属性下混用。

---

## 快速检查清单

- [ ] 第一步：域名 Nameservers 已改为 Cloudflare，SSL 为 HTTPS。
- [ ] 第二步：代码已推送到 GitHub，并在 Vercel 或 GitHub Pages 部署；自定义域名已绑定并生效。
- [ ] 第三步：本占位页文案为原创、未与其他站复制；若加 GA/GSC，本站使用独立 ID，不与其他域名共用。

完成以上三项后，占位页即可用于「养站」并避免被当作停放页或克隆站处理。
