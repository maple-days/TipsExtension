<p align="center">
  <a href="https://github.com/maple-days"><img src="https://github.com/maple-days.png?size=96" width="56" alt="Maple Days"></a>
</p>

<p align="center">
  <sub><b>Maple Days</b> · 《打脸日常》整合包 mod · Minecraft 1.12.2</sub>
</p>

# Tips Extension

给 **Tips** 模组加两样东西：**暂停菜单里的提示语**，以及**呼吸灯（渐隐渐显）效果**。

支援 Minecraft **1.12.2** ｜ 需要 **Tips** 作为前置 ｜ 纯客户端

---

## 特性

### 一、暂停菜单提示语

按下 **ESC** 时，提示语会显示在暂停菜单下方——原本这些提示只在加载界面出现。

- 每次打开暂停菜单会**换一条**提示
- 可以设置自动刷新，按秒数轮换
- 内容完全来自 Tips 模组，**你原有的提示文本不用改一行**

> 这个功能在 **Darkhax 的 Tips 更高版本**中已经存在，但 1.12.2 版的 Tips 没有。
> 本模组把同样的功能带到了 1.12.2，并额外增加了呼吸灯效果与独立的位置配置。

### 二、呼吸灯效果

给提示语加上平滑的明暗循环。

- **支持独立的位置设置**：暂停菜单里的位置与加载界面互不干扰
- 速度、最暗/最亮透明度都可调
- 标题不参与呼吸，只有正文渐隐渐显

## 效果预览

| 配置界面 | 暂停菜单里的提示 |
|---|---|
| ![配置](https://cdn.modrinth.com/data/cached_images/ab930d6b948a407c513dd58a285f7e245030dde0.png) | ![暂停提示](https://cdn.modrinth.com/data/cached_images/cd0606393ecdb8ec39ce6938272aba4228976810_0.webp) |

| 呼吸效果选项 | 实际效果 |
|---|---|
| ![呼吸选项](https://cdn.modrinth.com/data/cached_images/517cb04f826c60c98cafefe09252eadb543a1ad2.png) | ![呼吸](https://cdn.modrinth.com/data/cached_images/cc4b9dfdf46ead8b3350eadd0130e1cf4ffb7ec0.gif) |

## 这个模组为什么存在

1.12.2 的 Tips 只有加载界面的提示语，**暂停菜单提示是更高版本才加入的功能**。
为了在 1.12.2 上也能用，我按上游的设计实现了它；在此基础上又加了呼吸灯效果，
以及一套独立于加载界面的位置设置（上游的位置参数是给加载界面调校的，直接复用会压在暂停按钮上）。

## 配置

游戏内：**模组 → Tips Extension → 配置**（三个分页）
文件：`config/tipsext.cfg`

### 常规

| 键 | 默认 | 说明 |
|---|---|---|
| `showPauseTips` | `true` | 是否在暂停菜单显示提示语 |
| `enableAutoRefresh` | `true` | 是否按间隔自动更换提示 |
| `refreshInterval` | `5` | 更换间隔（秒），1–3600 |

### 暂停界面位置

| 键 | 默认 | 说明 |
|---|---|---|
| `pauseX` | `4` | 距屏幕左边缘（GUI 缩放后的像素） |
| `pauseY` | `4` | 距屏幕**底边**；整块底部对齐，标题向上展开 |
| `pauseWidthPercent` | `50` | 正文最大宽度，占屏幕宽度的百分比 |

> 早于 1.1.2 的版本直接复用 Tips 自己的 `xOffset/yOffset`，而那组数值是给加载界面调的，
> 结果提示语会压在「保存并退回到标题画面」按钮上。现在两套位置完全独立。

### 呼吸效果

| 键 | 默认 | 说明 |
|---|---|---|
| `enableFade` | `true` | 是否启用呼吸效果 |
| `fadeSpeed` | `800.0` | 数值越大越慢（毫秒级） |
| `minAlpha` | `0.4` | 最暗时的透明度 |
| `maxAlpha` | `1.0` | 最亮时的透明度 |

## 兼容性

| 项 | 值 |
|---|---|
| Minecraft | 1.12.2 |
| 加载器 | Forge 14.23.5.2847+ / Cleanroom |
| 环境 | **仅客户端** |
| 前置 | [Tips](https://www.curseforge.com/minecraft/mc-mods/tips)（必需） |
| 编译目标 | Java 8 |

多人服务器不需要装（纯客户端功能）。

## 构建

```bash
./gradlew build
# 产物：build/libs/tipsext-<version>.jar
```

Gradle 需要 JDK 17+ 运行，编译目标为 Java 8。

## 实现说明

- 暂停菜单的绘制走 `GuiScreenEvent.DrawScreenEvent.Post`，只处理 `GuiIngameMenu`
- 透明度直接编码进 `drawString` 的颜色参数——1.12.2 的 `FontRenderer` 会从颜色高 8 位读取 alpha
- **不修改 Tips 模组的任何静态配置**：早先的版本会改写 `Config.textColor`，那会污染游戏内所有使用 Tips 的渲染点
- 呼吸动画用 `System.nanoTime()` 而非 `currentTimeMillis()`，避免系统时钟调整导致跳变
- `minAlpha` 会被钳到 1/255 以上：`FontRenderer` 在 alpha 为 0 时会强制改回 255

## 致谢

- **Darkhax** —— 原始 [Tips](https://github.com/Darkhax-Minecraft/Tips) 模组；
  暂停菜单提示语这一设计来自其更高版本，本模组是 1.12.2 上的实现
- **MFn233** —— 暂停菜单渲染、呼吸效果与 1.1.x 维护

> 本模组的开发过程中使用了 AI 工具辅助（代码审查与问题定位）。

## 许可

MIT