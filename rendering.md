# 渲染：兼容有生图和无生图的 Agent

## 总规则

1. **母版 = SVG**（路径、基本形状、文字可转 path）。
2. 生图可选，用于阶段 3–4 的探索，不用于阶段 9 的母版。
3. 不绑定任何一家模型、MCP、付费 API、本机 GUI。
4. 用户工作区以外的字体、图片、品牌包，未提供就不要写死路径。

## 轨 A：矢量（所有能写文件的 Agent）

在项目内建立例如 `logo/`（名称可变）：

```text
logo/
  brief.md
  research.md
  concepts/
    a-wordmark.svg
    b-monogram.svg
    c-symbol.svg
  final/
    logo-color.svg
    logo-on-light.svg
    logo-on-dark.svg
    logo-mono.svg
  preview.html
  usage.md
```

### SVG 约定

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 64 64" role="img" aria-label="组织名称">
  <!-- 只用 path / circle / rect / polygon；定稿后文字转 path 或嵌入可商用字体 -->
</svg>
```

- `viewBox` 紧贴图形；留白写在使用规范里，不要靠 viewBox 外的随机空白冒充安全区（安全区用透明边或单独 `*-safe.svg`）。
- 颜色用明确 hex，不要 `currentColor` 当主交付（可另给一版 `currentColor` 供网站）。
- 一个文件一个版本（彩色 / 单黑 / 单白），不要靠 CSS 媒体查询切换当印刷稿。
- 避免滤镜、`foreignObject`、外链图片。外链字体会在别人打开时丢失——定稿把字母转 path，或在 `usage.md` 写明字体文件与授权。

### 不会手绘 path 时

仍要出 SVG，用圆与矩形搭几何字母，或用 `<text>` 做**可编辑草稿**，并在文件头注释：

```xml
<!-- DRAFT: text remains live. Convert to outlines before print. Font: <name> <license>. -->
```

活字草稿可以进入方向确认，不能当印刷交付。

### 预览页

写一个**无构建工具**的 `preview.html`，展示浅底、深底、圆裁、32px、16px、灰度。不依赖 npm/pnpm。

**必须把评审用的 SVG 内联进 HTML**（`<svg>...</svg>` 写在页面里），不要用 `<img src="concepts/….svg">` / `<object>` / 外链当预览页的唯一展示方式。

原因：Cursor 内嵌预览、部分 IDE 简易浏览器、以及把 HTML 当附件打开时，**相对路径资源经常解析失败**，用户只会看到裂图，误以为没出稿。独立的 `concepts/*.svg`、`final/*.svg` 仍要另存为源文件；预览页负责「打开即可见」。

深色底、单白版本：**单独画一版反色路径**，不要依赖 `filter: invert()` 去翻浅底稿（滤镜在部分预览里也不稳定）。

打开方式：优先系统浏览器；若用户在 IDE 里裂图，先自查是否误用了相对 `img`，再让用户用浏览器打开同一 `preview.html`。

## 轨 B：栅格探索（仅当工具存在）

「生图工具」包括但不限于：对话界面的生图、图像编辑、外部 API。用你**实际拥有的那一个**，不要安装新服务。

用途：情绪板、不成形的形态探索、向非设计决策人示意「气质」。

提示词约束（写入生图描述，不要写成长文）：

- 平面标志、纯色、白底或透明意向、无阴影、无透视、无照片
- 单一概念；黑白优先
- 写清名称字母、禁止项（无渐变、无 3D）
- 方形构图，主体居中，四周留白

出图后必须：

1. 选出可几何化的 1 个方向（能用圆和直线复述）。
2. 按轨 A **重画 SVG**。不要描摹照片噪点。
3. 探索 PNG 放 `concepts/`，文件名含 `explore-`，避免和 `final/` 混淆。

无生图工具：跳过本轨，用文字方向卡 + SVG 草稿代替。对用户说明「本环境无生图，直接用矢量草图评审」，然后继续。

## 轨 C：不能写文件

输出 Brief、方向卡、构造说明（网格、笔画粗细、色值、测试要求）。请用户保存，或在可写环境重跑轨 A。

## 导出 PNG / PDF（尽力而为）

按下面顺序尝试，**成功一种即可**，不要为导出安装庞大依赖：

1. 用户或 Agent 能打开 `preview.html` 时：说明用浏览器截图/打印 PDF。
2. 系统已有 `rsvg-convert`、Inkscape、ImageMagick 时：把 SVG 转 PNG（至少 512 与 1024，透明底）。
3. 都没有：交付 SVG + 预览 HTML，在 `usage.md` 写「PNG 未生成，用以上任一方式导出」。

不要仅仅因为不能转 PNG 就宣布项目失败。

## 与具体 Agent 的关系

| 环境特征 | 做法 |
|---|---|
| 只有文本 | 轨 C |
| 能写 SVG/HTML | 轨 A（完整交付） |
| 另有生图 | 轨 B → 轨 A |
| 有浏览器 | 打开 preview.html 做应用测试 |
| 有设计软件 | SVG 可再交给其精修字距；母版仍从 SVG 来 |

Cursor / Claude / Codex 等名称不要写进硬性步骤。技能必须在只实现「读、写、改文本文件」的 Agent 上可跑完轨 A 或轨 C。
