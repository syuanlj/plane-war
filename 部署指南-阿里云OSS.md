# 飞机大战游戏 - 阿里云OSS部署指南

域名：sixip.cn
游戏访问地址建议：game.sixip.cn

---

## 第一步：创建OSS Bucket

### 登录阿里云OSS控制台
https://oss.console.aliyun.com/

### 创建Bucket配置
| 配置项 | 值 |
|--------|-----|
| **Bucket名称** | `plane-war-sixip` (全局唯一，如被占用请修改) |
| **地域** | 华东1（杭州）推荐 |
| **存储类型** | 标准存储 |
| **存储冗余** | 本地冗余存储（LRS） |
| **版本控制** | ❌ 不开启 |
| **读写权限** | **✅ 公共读** (重要！必须选择公共读) |
| **服务器端加密** | ❌ 不开启 |
| **合规保留策略** | ❌ 不开启 |

---

## 第二步：上传游戏文件

1. 进入创建的Bucket
2. 点击左侧"文件管理"
3. 点击"上传文件"
4. 选择文件：`E:\openclaw\workspace\games\plane-war.html`
5. 点击"上传"
6. 等待上传完成

---

## 第三步：开启静态网站托管

1. 在Bucket左侧菜单，找到"数据管理"
2. 点击"静态页面"
3. 点击"设置"
4. **开启静态网站托管**
5. 配置：
   - **默认首页**：`plane-war.html`
   - **默认404页**：`plane-war.html` (可选，让404也跳转到游戏)
6. 点击"保存"

保存后会显示静态网站的默认访问地址，例如：
`http://plane-war-sixip.oss-cn-hangzhou.aliyuncs.com`

**提示：先访问这个地址测试，确保游戏可以正常打开**

---

## 第四步：绑定自定义域名

### 在OSS中添加域名
1. 在"静态页面"设置中，找到"自定义域名"
2. 点击"添加域名"
3. 输入：`game.sixip.cn`
4. 点击"确认"

系统会分配一个CNAME地址，类似：
`plane-war-sixip.oss-cn-hangzhou.aliyuncs.com.cdn.dnsv1.com`

**记录这个CNAME地址，第五步DNS解析要用！**

---

## 第五步：配置DNS解析

### 登录阿里云域名控制台
https://dc.console.aliyun.com/

### 添加解析记录
1. 找到域名 `sixip.cn`，点击"解析"
2. 点击"添加记录"
3. 配置：

| 记录类型 | 主机记录 | 记录值 | TTL |
|----------|----------|--------|-----|
| CNAME | game | plane-war-sixip.oss-cn-hangzhou.aliyuncs.com.cdn.dnsv1.com | 10分钟 |

**注意**：
- 记录值填写第四步中OSS提供的CNAME地址
- 主机记录 `game` 表示访问 `game.sixip.cn`
- 如果想直接访问 `sixip.cn`，主机记录填写 `@`

4. 点击"确认"

---

## 第六步：等待DNS生效

DNS解析生效时间：10分钟 ~ 2小时（通常10分钟内）

### 检查DNS是否生效
在PowerShell中执行：
```powershell
nslookup game.sixip.cn
```

如果返回OSS的CNAME地址，说明已生效。

---

## 第七步：添加HTTPS（可选，强烈推荐）

### 方式A：使用阿里云免费SSL证书

1. 登录阿里云SSL证书控制台
   https://yundun.console.aliyun.com/?p=cas

2. 点击"免费证书" → "立即购买"
3. 选择："单域名证书"、"DV证书"、"1年"
4. 点击"立即购买" → "支付"（免费，0元）

5. 购买后，点击"证书申请"
6. 填写信息：
   - 域名：`game.sixip.cn`
   - 其他按默认
7. 点击"验证"（DNS自动验证）
8. 等待几分钟，证书签发成功

9. 点击"下载"，选择"Nginx"

10. 在OSS Bucket的"静态页面" → "自定义域名"中
    - 找到 `game.sixip.cn`
    - 点击"证书托管"
    - 上传下载的证书文件

### 方式B：使用阿里云CDN自动HTTPS

1. 为域名配置阿里云CDN（更复杂，但更快）
2. CDN会自动配置HTTPS证书

---

## 第八步：测试访问

访问：https://game.sixip.cn

如果游戏正常打开，说明部署成功！🎉

---

## 常见问题

### Q1: 访问显示403错误
**原因**：Bucket读写权限不是公共读
**解决**：在OSS控制台，Bucket → 权限控制 → 读写权限 → 设置为"公共读"

### Q2: DNS解析不生效
**原因**：等待时间不够或配置错误
**解决**：
1. 用 `nslookup game.sixip.cn` 检查
2. 清除本地DNS缓存：`ipconfig /flushdns`
3. 检查CNAME地址是否正确

### Q3: 游戏无法访问
**原因**：静态页面未开启或配置错误
**解决**：
1. 确认已开启"静态网站托管"
2. 默认首页设置为 `plane-war.html`
3. 确认文件已上传成功

### Q4: HTTPS证书配置失败
**原因**：DNS验证未通过
**解决**：
1. 等待DNS解析生效后再申请证书
2. 检查DNS记录是否正确

---

## 费用说明

### OSS费用
- **存储费用**：约 0.12元/GB/月（游戏文件很小，几乎免费）
- **请求费用**：约 0.01元/万次请求（每天1000次访问，月费用约0.03元）
- **流量费用**：约 0.5元/GB（游戏很小，流量很少）

**预计每月费用：不到1元**

### SSL证书
- **免费证书**：0元（每域名每年1个免费证书）

---

## 部署检查清单

- [ ] 创建Bucket，读写权限设置为公共读
- [ ] 上传 plane-war.html 文件
- [ ] 开启静态网站托管，默认首页设置为 plane-war.html
- [ ] 测试默认OSS地址可访问
- [ ] 添加自定义域名 game.sixip.cn
- [ ] 配置DNS解析（CNAME记录）
- [ ] 等待DNS生效，用nslookup检查
- [ ] 访问 game.sixip.cn 测试
- [ ] 配置HTTPS证书（可选）
- [ ] 测试HTTPS访问

---

## 后续维护

### 更新游戏
1. 修改游戏代码
2. 在OSS中上传新版本（覆盖原文件）
3. 客户端刷新即可更新

### 备份
游戏代码在 `E:\openclaw\workspace\games\plane-war.html`
建议定期备份到Git或其他位置

---

**部署完成后，游戏地址：https://game.sixip.cn**