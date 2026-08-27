# 🌳 开放世界 3D 点云概念树 · 交互 Demo

> 不靠人工标注，从无标签点云中自动学习一棵**可解释的概念树**——让机器不仅能分类，还能**描述物体、归纳概念、认识从未见过的东西**。

这是一个纯静态、零依赖的网页 demo，直接双击 `index.html` 或部署到 GitHub Pages 即可运行。

## 页面

| 页面 | 内容 |
|---|---|
| [`index.html`](index.html) | 系统总览：流程、核心结果、技术亮点 |
| [`ood.html`](ood.html) | **认识未见物体**：7 个训练集外物体（扶手椅/餐桌/毛巾/厨师机/乒乓球拍/纸巾架/信件）的三层概念画像 |
| [`tree.html`](tree.html) | **交互式概念树浏览器**：2172 节点可展开、可搜索，点节点看成员 RGB 图 / 类分布 / 物理概念 / 语言描述 |

## 数据结构

```
demo/
├── index.html        # 落地页
├── tree.html         # 概念树浏览器
├── ood.html          # OOD 认识 demo
├── data/
│   ├── tree_demo.json  # 概念树（2172 节点：id/深度/父/标签/纯度/尺寸/概念/紧密度/类分布/属性描述/图）
│   └── ood_demo.json   # 7 个 OOD 物体画像（路径/CLIP/物理/命中叶）
└── assets/             # 节点 RGB 渲染 + OOD 渲染（共 87 张）
```

页面通过 `fetch('data/...json')` 加载数据，因此**需要通过 HTTP 服务访问**（`file://` 下浏览器会拦截 fetch）。

## 本地运行

```bash
cd demo && python3 -m http.server 8000
# 打开 http://localhost:8000
```

## 部署到 GitHub Pages（5 分钟）

```bash
# 1. 在 GitHub 新建一个空仓库（如 concept-tree-demo）
cd demo
git init
git add .
git commit -m "open-world point cloud concept tree demo"

# 2. 推送到远程
git remote add origin https://github.com/<你的用户名>/concept-tree-demo.git
git branch -M main
git push -u origin main

# 3. 开启 Pages
#    GitHub 仓库 → Settings → Pages → Source: Deploy from a branch
#    Branch: main / root → Save
#    等 1-2 分钟，访问 https://<你的用户名>.github.io/concept-tree-demo/
```

> 或者整个项目仓库一起推（含 src/ 代码、docs/ 复盘），demo 就在仓库根目录的 `demo/` 子目录，Pages 设置同样选 main/root 即可，访问 `<user>.github.io/<repo>/demo/`。

## 数据说明

- 预训练：Point-CMAE + VICReg（ARID + ScanNet，52,520 实例，自监督，无标签）
- 概念树：256D 特征 → UMAP 30D → 递归 HDBSCAN（2172 节点，595 内部 + 1577 叶，深 6）
- 可解释层：12 维确定性物理描述子 + CLIP 语言属性投影（shape/color/size/material/abstract）
- OOD：7 个 ScanNet 61 类之外的物体，仅用已学概念树 + 物理 + 语言去"认识"它们

