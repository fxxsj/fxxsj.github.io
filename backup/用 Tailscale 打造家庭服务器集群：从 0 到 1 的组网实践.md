# 用 Tailscale 打造家庭服务器集群：从 0 到 1 的组网实践

> **场景**：家里有 Mac mini，云上有两台 Linux 服务器，想让他们像在一个局域网一样互相访问。

> **方案**：Tailscale - 一款基于 WireGuard 的零配置 VPN 工具。

---

## 什么是 Tailscale？

Tailscale 是一款**零配置 VPN** 工具，基于 WireGuard 协议。它的核心优势：

- ✅ **无需公网 IP**：即使你的设备在 NAT 后面
- ✅ **自动打洞**：P2P 直连，不走服务器中转
- ✅ **跨平台**：macOS、Linux、Windows、Android、iOS 全支持
- ✅ **安全加密**：端到端加密，私钥不上传
- ✅ **免费额度**：个人使用免费，支持 100 台设备

---

## 我的网络架构

### 设备清单

| 设备名 | 角色 | 系统 |
|--------|------|------|
| **Mac mini (基地)** | 家庭服务器 | macOS |
| **云服务器 A (桥梁)** | API 中转站 | Linux |
| **云服务器 B (利剑)** | 应用服务器 | Linux |
| **NUC** | 家庭节点 | Linux |
| **MacBook Pro** | 工作本 | macOS |
| **Android 手机** | 移动设备 | Android |

**注**：IP 地址已脱敏，实际部署时 Tailscale 会自动分配 100.x.x.x 网段。

### 网络拓扑

```
┌─────────────────────────────────────────────────────────────┐
│                      Tailscale 网络段                        │
│                        100.x.x.x                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐  │
│  │   基地      │     │   桥梁      │     │   利剑      │  │
│  │  Mac mini   │────│  云服务器 A │────│  云服务器 B │  │
│  │  家庭       │     │   中转站    │     │   应用      │  │
│  └─────────────┘     └─────────────┘     └─────────────┘  │
│                                                              │
│  ┌─────────────┐     ┌─────────────┐                      │
│  │ MacBook Pro │     │  Android手机 │                      │
│  └─────────────┘     └─────────────┘                      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 安装配置

### 1. 注册账号

访问 [Tailscale 官网](https://tailscale.com)，用 Google/GitHub 账号登录即可。

### 2. 安装客户端

**macOS / Linux:**
```bash
# macOS
brew install --cask tailscale

# Ubuntu/Debian
curl -fsSL https://tailscale.com/install.sh | sh

# Arch Linux
yay -S tailscale
```

**Android / iOS:**
在应用商店搜索 "Tailscale" 安装。

### 3. 登录并连接

```bash
# macOS/Linux
sudo tailscale up
```

第一次运行会弹出浏览器，登录后自动加入网络。

---

## 实战应用场景

### 场景 1：SSH 免密登录云服务器

**问题**：云服务器没有公网 IP，每次需要跳板机才能 SSH。

**解决**：通过 Tailscale 直连。

```bash
# 直接用 Tailscale IP 连接（IP 已脱敏）
ssh root@100.x.x.x

# 或用设备名（需配置 MagicDNS）
ssh root@server-name
```

**优势**：
- 不需要公网 IP
- 不需要开放端口
- 跳板机成为历史

---

### 场景 2：内网服务外网化

**问题**：Mac mini 上的服务只能在局域网访问，出门后无法使用。

**解决**：Tailscale 让所有设备都在"虚拟局域网"中。

```bash
# 在公司通过 Tailscale 访问家里的 Mac mini
ssh username@100.x.x.x

# 访问 Mac mini 上的 Web 服务
curl http://100.x.x.x:8080
```

---

### 场景 3：服务器集群组网

**问题**：云服务器之间需要互相通信，但安全组限制较多。

**解决**：Tailscale 让它们像在同一个机房。

```bash
# 服务器 A 访问服务器 B
ssh root@100.x.x.x

# 同步文件
rsync -avz /data/ root@100.x.x.x:/backup/
```

---

### 场景 4：手机管理服务器

**问题**：出门在外，需要紧急登录服务器。

**解决**：手机上的 Tailscale + Termius（SSH 客户端）。

```bash
# 在手机上直接连接
ssh root@100.x.x.x
```

---

## 高级配置

### 1. ACL 访问控制

不同设备设置不同的访问权限：

```json
// ACL 示例（脱敏）
{
  "groups": {
    "group:admins": ["user:you@example.com"],
    "group:servers": ["server-a", "server-b"]
  },
  "acls": [
    {"action": "accept", "src": ["group:admins"], "dst": ["group:servers:*"]},
    {"action": "accept", "src": ["group:admins"], "dst": ["*:*"]}
  ]
}
```

### 2. Subnet Router（子网路由）

**场景**：让 Tailscale 网络访问整个云服务器所在的内网。

```bash
# 在云服务器上启用子网路由
sudo tailscale up --advertise-routes=10.0.0.0/24

# 在其他设备上接受路由
sudo tailscale up --accept-routes=10.0.0.0/24
```

### 3. Exit Node（出口节点）

**场景**：出门在外，通过家里的 Mac mini 上网（保护隐私）。

```bash
# Mac mini 上启用出口节点
sudo tailscale up --advertise-exit-node

# 手机上选择 Mac mini 作为出口节点
```

---

## 性能优化

### 1. DERP 中继服务器

Tailscale 在国内访问速度较慢，可以配置 DERP 服务器：

```json
{
  "derpMap": {
    "OmitDefaultRegions": true,
    "Regions": {
      "900": {
        "RegionID": 900,
        "RegionCode": "cn",
        "RegionName": "China",
        "Nodes": [
          {
            "Name": "cn1",
            "RegionID": 900,
            "HostName": "your-derp-server.com"
          }
        ]
      }
    }
  }
}
```

**注**：可以使用香港 VPS 搭建 DERP 服务器提升国内访问速度。

### 2. MTU 设置

网络性能不佳时，调整 MTU：

```bash
# macOS
sudo ifconfig utun0 mtu 1280

# Linux
sudo ip link set dev tailscale0 mtu 1280
```

---

## 安全建议

1. **启用 ACL**：限制设备间的访问权限
2. **定期审计设备列表**：移除不用的设备
3. **密钥管理**：私钥本地存储，不要泄露
4. **监控日志**：Tailscale 管理后台可查看连接日志
5. **保护配置文件**：ACL 等配置包含敏感信息，注意保密

---

## 常见问题

**Q: Tailscale 在国内速度慢？**

A: 可以自建 DERP 中继服务器，或者使用香港节点的 VPS。

**Q: 如何退出网络？**

A: 
```bash
# 退出但保留配置
sudo tailscale down

# 完全退出并删除设备
sudo tailscale logout
```

**Q: 免费版够用吗？**

A: 个人使用完全够用，100 台设备额度很大。

**Q: 如何查看设备 IP？**

A: 
```bash
# 列出所有设备
tailscale status

# 查看当前设备 IP
tailscale ip
```

---

## 总结

Tailscale 让多台组网变得异常简单：

- ✅ **零配置**：安装即用，无需折腾路由器
- ✅ **安全可靠**：端到端加密，私钥不上传
- ✅ **跨平台**：全平台支持，手机电脑无缝切换
- ✅ **免费够用**：个人使用 100 台设备免费

**适用场景**：
- 家庭服务器远程管理
- 云服务器集群组网
- 移动办公安全访问
- 多设备文件同步

如果你也有多台设备需要组网，强烈推荐试试 Tailscale！

---

**相关资源**：

- [Tailscale 官网](https://tailscale.com)
- [Tailscale 文档](https://tailscale.com/kb)
- [DERP 服务器搭建](https://tailscale.com/kb/1118/derp)

---

**隐私说明**：本文中的 IP 地址、域名等敏感信息已脱敏处理，实际部署时请根据实际情况调整。
