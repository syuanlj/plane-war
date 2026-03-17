# 飞机大战游戏 - GitHub Pages部署指南

域名：sixip.cn

---

## 第一步：推送代码到GitHub

### 1.1 确认本地代码已提交
```powershell
cd E:\openclaw\workspace\games
git status
```

### 1.2 创建GitHub Personal Access Token

**注意：之前的token已暴露，需要撤销并重新创建！**

1. 访问：https://github.com/settings/tokens
2. 撤销旧的token（点击Delete）
3. 点击"Generate new token" → "Generate new token (classic)"
4. 配置：
   - **Note**：`plane-war`
   - **Expiration**：90 days（建议）
   - **勾选权限**：
     - ✅ `repo`（完整仓库访问权限）
     - ✅ `workflow`（如果需要GitHub Actions）
5. 点击"Generate token"
6. **重要**：立即复制token（只显示一次！）

### 1.3 推送代码到GitHub

**方法A：命令行推送（推荐）**
```powershell
cd E:\openclaw\workspace\games
git push -u origin main
```
当提示输入用户名和密码时：
- **Username**：`syuanlj`
- **Password**：**粘贴刚才复制的token**（不是GitHub登录密码）

**方法B：使用Git凭据助手（一次配置，以后免输入）**
```powershell
# 配置Git凭据助手
git config --global credential.helper wincred

# 推送代码
git push -u origin main
```
输入token后会自动保存。

### 1.4 验证推送成功
访问：https://github.com/syuanlj/plane-war
如果能看到 `plane-war.html` 文件，说明推送成功！

---

## 第二步：启用GitHub Pages

### 2.1 进入仓库设置
1. 访问：https://github.com/syuanlj/plane-war
2. 点击仓库顶部的 **Settings**

### 2.2 配置Pages
1. 在左侧菜单中找到 **Pages**
2. 配置：
   - **Source**：Deploy from a branch
   - **Branch**：`main` / `/(root)`（选择main分支和根目录）
3. 点击 **Save**

### 2.3 等待部署
- 部署通常需要1-3分钟
- 页面会显示部署状态：`Deploying...` → `✅ Deployed`

### 2.4 获取游戏地址
部署成功后，会显示访问地址：
```
https://syuanlj.github.io/plane-war/
```

**测试访问这个地址，确认游戏正常运行！**

---

## 第三步：绑定自定义域名 game.sixip.cn

### 3.1 在GitHub Pages中添加自定义域名

1. 在Pages设置页面
2. 找到 **Custom domain**
3. 输入：`game.sixip.cn`
4. 点击 **Save**

5. **重要**：勾选 **Enforce HTTPS**（启用HTTPS）
   - 需要先配置DNS解析才能启用

### 3.2 配置DNS解析

#### 方法A：使用阿里云DNS（推荐）

1. 登录阿里云域名控制台：https://dc.console.aliyun.com/
2. 找到域名 `sixip.cn`，点击"解析"
3. 点击"添加记录"
4. 配置：

| 记录类型 | 主机记录 | 记录值 | TTL |
|----------|----------|--------|-----|
| CNAME | game | syuanlj.github.io. | 10分钟 |

**注意**：
- 记录值中的 `syuanlj.github.io.` **末尾有英文句点**（.）
- 主机记录 `game` 表示访问 `game.sixip.cn`
- 如果想直接访问 `sixip.cn`，主机记录填写 `@`

5. 点击"确认"

#### 方法B：使用其他DNS服务商

如果是腾讯云、Cloudflare等，配置相同：
- **记录类型**：CNAME
- **主机记录**：game
- **记录值**：`syuanlj.github.io.`（注意末尾有.）

### 3.3 DNS解析检查

在PowerShell中执行：
```powershell
nslookup game.sixip.cn
```

如果返回：
```
CNAME syuanlj.github.io
```
说明DNS解析成功！

---

## 第四步：启用HTTPS

### 4.1 在GitHub Pages中启用HTTPS

1. 回到仓库的Pages设置
2. 找到 **Custom domain**
3. 确保 **Enforce HTTPS** 被勾选
4. GitHub会自动为您的域名配置SSL证书（免费）

### 4.2 等待证书签发

证书签发通常需要5-10分钟，有时需要1小时。

如果长时间未签发：
1. 检查DNS解析是否正确
2. 确保域名已备案（.cn域名需要备案）
3. 刷新Pages设置页面

---

## 第五步：测试访问

### 访问游戏地址
```
https://game.sixip.cn
```

### 检查清单
- [ ] 页面能正常打开
- [ ] 游戏可以正常玩
- [ ] 音效正常播放
- [ ] 地址栏显示HTTPS（小锁图标）
- [ ] 分数和生命值正常显示

---

## 常见问题

### Q1: 推送时提示 Authentication failed
**原因**：token无效或过期
**解决**：
1. 检查token是否正确复制
2. 确认token有 `repo` 权限
3. 尝试重新生成token

### Q2: GitHub Pages显示 404 Not Found
**原因**：文件路径错误或未部署
**解决**：
1. 确认文件在仓库根目录
2. 文件名是 `plane-war.html`（不是index.html）
3. 等待部署完成（1-3分钟）
4. 检查Pages设置中的Branch和目录

### Q3: 自定义域名访问失败
**原因**：DNS解析未生效或配置错误
**解决**：
1. 用 `nslookup game.sixip.cn` 检查DNS
2. 确认CNAME记录值是 `syuanlj.github.io.`（注意末尾.）
3. 等待DNS生效（10分钟~2小时）
4. 清除本地DNS缓存：`ipconfig /flushdns`

### Q4: HTTPS证书一直显示 Pending
**原因**：DNS未正确配置或域名未备案
**解决**：
1. 检查DNS解析是否正确
2. .cn域名需要备案（您已备案）
3. 解析生效后，等待10-15分钟
4. 在Pages设置中取消勾选Enforce HTTPS，重新勾选

### Q5: 音效无法播放
**原因**：浏览器自动播放策略限制
**解决**：
- 需要用户与页面交互后才能播放音效
- 点击"开始游戏"后音效正常
- 这是浏览器安全机制，非代码问题

---

## 费用说明

### GitHub Pages
- ✅ **完全免费**
- ✅ 无存储限制
- ✅ 无流量限制
- ✅ 自动HTTPS证书
- ✅ GitHub CDN加速

### GitHub免费账户
- ✅ 公开仓库：免费
- ✅ 私有仓库：免费（有某些限制）

**总费用：0元**

---

## 优势对比

### GitHub Pages vs 阿里云OSS

| 特性 | GitHub Pages | 阿里云OSS |
|------|--------------|-----------|
| **费用** | 免费 | 约1元/月 |
| **速度** | 较快 | 很快 |
| **配置** | 简单 | 较复杂 |
| **HTTPS** | 自动免费证书 | 需手动配置 |
| **CDN** | GitHub CDN | 阿里云CDN |
| **域名绑定** | 支持自定义域名 | 支持自定义域名 |
| **CI/CD** | 支持GitHub Actions | 需要额外配置 |
| **版本控制** | Git集成 | 需手动上传 |
| **适合场景** | 开源项目、个人博客 | 企业网站、大流量 |

**推荐：个人项目首选GitHub Pages，简单免费！**

---

## 更新游戏

### 方法A：直接修改并推送（推荐）

1. 修改游戏代码：
   ```powershell
   notepad E:\openclaw\workspace\games\plane-war.html
   ```

2. 提交更改：
   ```powershell
   cd E:\openclaw\workspace\games
   git add plane-war.html
   git commit -m "fix: 修复bug/更新功能"
   git push
   ```

3. GitHub Pages自动部署（1-3分钟）

### 方法B：GitHub网页编辑

1. 访问：https://github.com/syuanlj/plane-war
2. 点击 `plane-war.html`
3. 点击右上角 ✏️ 编辑
4. 修改代码
5. 提交更改（Commit changes）

---

## 进阶配置

### 启用GitHub Actions自动部署

创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: .
```

**效果**：每次推送代码，自动触发部署流程。

---

## 部署检查清单

- [ ] 推送代码到GitHub成功
- [ ] GitHub仓库能看到 plane-war.html
- [ ] 启用GitHub Pages（Branch: main）
- [ ] 等待部署完成
- [ ] 访问 https://syuanlj.github.io/plane-war/ 测试
- [ ] 添加自定义域名 game.sixip.cn
- [ ] 配置DNS解析（CNAME到syuanlj.github.io.）
- [ ] 等待DNS生效，用nslookup检查
- [ ] 启用HTTPS
- [ ] 访问 https://game.sixip.cn 测试
- [ ] 测试游戏功能正常

---

## 后续维护

### 定期更新
- 修改bug、添加功能后直接推送
- GitHub Pages自动部署

### 监控
- GitHub提供访问统计（在Insights中）
- 可以查看流量、访问来源等

### 备份
- 代码已保存在GitHub，自动备份
- 本地代码在：`E:\openclaw\workspace\games\plane-war.html`

---

## 完成后访问地址

**GitHub默认地址：**
https://syuanlj.github.io/plane-war/

**自定义域名地址：**
https://game.sixip.cn

---

**推荐选择GitHub Pages，免费简单，完全够用！**