# S 项目部署到 GitHub Pages

本地已准备好：`git init` 完成、`index.html` + `poi_sz.json` 已提交（commit `8baeaa8`）。
剩下只差 **GitHub 认证 + 推送**。你按下面选一种方式，一次性配好后以后部署就是两三条命令。

---

## 方式 A：HTTPS + Personal Access Token（推荐，最简单）

### 第 1 步：生成 token
1. 打开 https://github.com/settings/tokens
2. **Generate new token → Classic**
3. Note 填 `github-pages-deploy`，Expiration 选 90 days
4. 勾选权限：**repo**（整个那一组勾上就够了）
5. 点 Generate，**复制那串 `ghp_` 开头的 token**（只显示一次）

### 第 2 步：创建远程仓库
在 GitHub 网页上新建一个空仓库：
- 名字：`shenzhen-guide`
- 公开（Public）
- **不要**勾 "Add a README"、不要加 .gitignore、不要选 license（保持完全空）

### 第 3 步：推送（把命令里的 TOKEN 换成你的）
```bash
cd "C:/Users/zhaozw/交建通WorkBuddy/2026-11-17-15-30-57/h-s-validation/S_pwa"
git remote add origin https://TOKEN@github.com/remmusb/shenzhen-guide.git
git branch -M main
git push -u origin main
```

### 第 4 步：开启 Pages
- 仓库页面 → **Settings** → **Pages**（左侧最下面）
- Build and deployment → Source 选 **Deploy from a branch**
- Branch 选 **main** → 文件夹选 **/(root)** → Save
- 等 1-2 分钟，访问 `https://remmusb.github.io/shenzhen-guide/`

> 安全提醒：token 只在这条命令里用一次。推完后可以执行 `git remote set-url origin https://github.com/remmusb/shenzhen-guide.git` 把 URL 里的 token 去掉（因为 `.git/config` 会明文存 remote URL）。

---

## 方式 B：SSH key（一劳永逸，但要配置一次）

### 第 1 步：生成 SSH key
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```
一路回车（密码可留空）。生成后：
```bash
cat ~/.ssh/id_ed25519.pub
```
把输出整段复制。

### 第 2 步：添加到 GitHub
https://github.com/settings/keys → **New SSH key** → 粘贴 → Add SSH key

### 第 3 步：创建远程仓库
同方式 A 第 2 步（`shenzhen-guide`，保持空）。

### 第 4 步：推送
```bash
cd "C:/Users/zhaozw/交建通WorkBuddy/2026-11-17-15-30-57/h-s-validation/S_pwa"
git remote add origin git@github.com:remmusb/shenzhen-guide.git
git branch -M main
git push -u origin main
```

### 第 5 步：开启 Pages
同方式 A 第 4 步。

---

## 部署后的验证清单

- [ ] `https://remmusb.github.io/shenzhen-guide/` 打开正常
- [ ] 地图显示 101 个点位
- [ ] 分类筛选（景点/餐厅/商圈）正常
- [ ] 点位详情里 YouTube 视频能嵌入播放
- [ ] 留资表单：填邮箱提交 → Supabase `leads` 表出现记录
- [ ] 手机浏览器打开正常（PWA 响应式）

---

## 日常更新流程

以后我改完代码后，你只需：
```bash
cd "C:/Users/zhaozw/交建通WorkBuddy/2026-11-17-15-30-57/h-s-validation/S_pwa"
git add -A && git commit -m "update" && git push
```
GitHub Pages 1-2 分钟后自动生效。

---

## 注意事项

- **key 是公开的**：`index.html` 里的 `sb_publishable_` key 是设计给前端用的，可以放公开仓库（官方明确说 can be safely shared publicly），受 RLS 保护。
- **不要提交 secret key**：`sb_secret_` 开头的一律不能进代码和仓库。
- **数据文件**：`poi_sz.json` 是从 27 条视频提取的 101 个 POI，将来视频量大了要重新生成（跑 `pipeline/fetch_youtube.py` → `build_sample_report.py` → `geocode_s.py` → `fix_geocode.py`，然后复制到这里）。
