---
title: 作品集
date: 2026-09-09 17:30:00
description: 我的 GitHub 公开仓库与作品 showcase
---

<!-- 静态策展页面：仓库数据由 GitHub API 抓取后手写于此，如需增删直接改下面的 .pk-repo 卡片 -->

<div class="pk-portfolio">

<style>
.pk-portfolio .pk-intro { color: #8a8a8a; margin-bottom: 1.6em; }
.pk-repo-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: 18px;
}
.pk-repo {
  border: 1px solid rgba(125,125,125,.2);
  border-radius: 12px;
  padding: 18px 18px 16px;
  background: #fff;
  box-shadow: 0 1px 3px rgba(0,0,0,.05);
  display: flex;
  flex-direction: column;
  gap: 8px;
  transition: transform .18s, box-shadow .18s;
}
.pk-repo:hover { transform: translateY(-3px); box-shadow: 0 8px 22px rgba(0,0,0,.10); }
.pk-repo-head { display: flex; align-items: center; justify-content: space-between; gap: 8px; }
.pk-repo-name { font-weight: 700; font-size: 1.02em; color: #3572ef; text-decoration: none; word-break: break-all; }
.pk-repo-name:hover { text-decoration: underline; }
.pk-repo-stars { font-size: .82em; color: #f2b01e; white-space: nowrap; }
.pk-repo-lang { font-size: .76em; color: #666; letter-spacing: .3px; text-transform: uppercase; }
.pk-repo-lang .pk-dot { display:inline-block; width:9px; height:9px; border-radius:50%; margin-right:5px; vertical-align:-1px; }
.pk-repo-desc { font-size: .86em; color: #555; line-height: 1.55; flex-grow: 1; }
.pk-repo-link { font-size: .82em; color: #3572ef; text-decoration: none; }
.pk-repo-link:hover { text-decoration: underline; }
@media (prefers-color-scheme: dark) {
  .pk-repo { background: rgba(255,255,255,.04); border-color: rgba(255,255,255,.08); }
  .pk-repo-desc { color: #b8b8b8; }
  .pk-repo-lang { color: #9a9a9a; }
  .pk-portfolio .pk-intro { color: #8f8f8f; }
}
</style>

<p class="pk-intro">以下收录我在 GitHub 上公开的仓库，可作为个人作品展示。（点击卡片右上 GitHub ↗ 可跳转）</p>

<div class="pk-repo-grid">

<!-- ---------- 项目 1: Sleep_Steward ---------- -->
<div class="pk-repo">
  <div class="pk-repo-head">
    <a class="pk-repo-name" href="https://github.com/pika-from-akachi/Sleep_Steward">Sleep_Steward</a>
    <span class="pk-repo-stars">⭐ 4</span>
  </div>
  <div class="pk-repo-lang"><span class="pk-dot" style="background:#3572ef"></span>Python</div>
  <p class="pk-repo-desc">NERO 机械臂视觉抓取 + Tracer 移动底盘 CAN 控制（RDK X5）。跨硬件联合项目。</p>
  <a class="pk-repo-link" href="https://github.com/pika-from-akachi/Sleep_Steward">GitHub ↗</a>
</div>

<!-- ---------- 项目 2: Wheel-Legged_Robot ---------- -->
<div class="pk-repo">
  <div class="pk-repo-head">
    <a class="pk-repo-name" href="https://github.com/pika-from-akachi/Wheel-Legged_Robot">Wheel-Legged_Robot</a>
    <span class="pk-repo-stars">⭐ 1</span>
  </div>
  <div class="pk-repo-lang"><span class="pk-dot" style="background:#555"></span>C</div>
  <p class="pk-repo-desc">轮足机器人项目，基于 STM32 HAL 开发。</p>
  <a class="pk-repo-link" href="https://github.com/pika-from-akachi/Wheel-Legged_Robot">GitHub ↗</a>
</div>

<!-- ---------- 项目 3: smart-traffic-cone ---------- -->
<div class="pk-repo">
  <div class="pk-repo-head">
    <a class="pk-repo-name" href="https://github.com/pika-from-akachi/smart-traffic-cone">smart-traffic-cone</a>
    <span class="pk-repo-stars">⭐ 0</span>
  </div>
  <div class="pk-repo-lang"><span class="pk-dot" style="background:#f1e05a"></span>JavaScript</div>
  <p class="pk-repo-desc">智能交通锥 monorepo：ESP32 固件 + 云端 API + 调度 Web demo。（fork）</p>
  <a class="pk-repo-link" href="https://github.com/pika-from-akachi/smart-traffic-cone">GitHub ↗</a>
</div>

<!-- ---------- 项目 4: InfusePlus ---------- -->
<div class="pk-repo">
  <div class="pk-repo-head">
    <a class="pk-repo-name" href="https://github.com/pika-from-akachi/InfusePlus">InfusePlus</a>
    <span class="pk-repo-stars">⭐ 0</span>
  </div>
  <div class="pk-repo-lang"><span class="pk-dot" style="background:#8b66ff"></span>iOS</div>
  <p class="pk-repo-desc">Infuse iOS 应用增强扩展。（fork）</p>
  <a class="pk-repo-link" href="https://github.com/pika-from-akachi/InfusePlus">GitHub ↗</a>
</div>

<!-- ---------- 项目 5: AIxOriginDemo ---------- -->
<div class="pk-repo">
  <div class="pk-repo-head">
    <a class="pk-repo-name" href="https://github.com/pika-from-akachi/AIxOriginDemo">AIxOriginDemo</a>
    <span class="pk-repo-stars">⭐ 0</span>
  </div>
  <div class="pk-repo-lang"><span class="pk-dot" style="background:#7f52ff"></span>Kotlin</div>
  <p class="pk-repo-desc">AI 相关 Demo 项目。</p>
  <a class="pk-repo-link" href="https://github.com/pika-from-akachi/AIxOriginDemo">GitHub ↗</a>
</div>

<!-- ---------- 项目 6: pika-from-akachi.github.io ---------- -->
<div class="pk-repo">
  <div class="pk-repo-head">
    <a class="pk-repo-name" href="https://github.com/pika-from-akachi/pika-from-akachi.github.io">github.io</a>
    <span class="pk-repo-stars">⭐ 0</span>
  </div>
  <div class="pk-repo-lang"><span class="pk-dot" style="background:#e34c26"></span>HTML</div>
  <p class="pk-repo-desc">GitHub Pages 站点。</p>
  <a class="pk-repo-link" href="https://github.com/pika-from-akachi/pika-from-akachi.github.io">GitHub ↗</a>
</div>

<!-- ---------- 项目 7: pika-from-akachi (profile) ---------- -->
<div class="pk-repo">
  <div class="pk-repo-head">
    <a class="pk-repo-name" href="https://github.com/pika-from-akachi/pika-from-akachi">GitHub Profile</a>
    <span class="pk-repo-stars">⭐ 1</span>
  </div>
  <div class="pk-repo-lang"><span class="pk-dot" style="background:#236"></span>README</div>
  <p class="pk-repo-desc">GitHub 个人主页 README 配置。</p>
  <a class="pk-repo-link" href="https://github.com/pika-from-akachi/pika-from-akachi">GitHub ↗</a>
</div>

<!-- ---------- 项目 8: images ---------- -->
<div class="pk-repo">
  <div class="pk-repo-head">
    <a class="pk-repo-name" href="https://github.com/pika-from-akachi/images">images</a>
    <span class="pk-repo-stars">⭐ 0</span>
  </div>
  <div class="pk-repo-lang"><span class="pk-dot" style="background:#999"></span>Assets</div>
  <p class="pk-repo-desc">博客 / 项目用图片资源集。</p>
  <a class="pk-repo-link" href="https://github.com/pika-from-akachi/images">GitHub ↗</a>
</div>

</div>
</div>
