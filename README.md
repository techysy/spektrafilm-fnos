<div align="center">

# spektrafilm-fnos

**把 spektrafilm 光谱胶片模拟装成飞牛 fnOS 应用：RAW 进、胶片模拟 + 放大印相出，浏览器直接用**

[![License](https://img.shields.io/badge/%E8%AE%B8%E5%8F%AF-GPL--3.0--or--later-f59e0b)](LICENSE)
[![Upstream](https://img.shields.io/badge/%E4%B8%8A%E6%B8%B8-spektrafilm-4d6bfe)](https://github.com/andreavolpato/spektrafilm)
[![Status](https://img.shields.io/badge/%E7%8A%B6%E6%80%81-%E6%90%AD%E5%BB%BA%E4%B8%AD-6b7280)](ROADMAP.md)

[快速开始](#快速开始) · [架构](#架构) · [许可与署名](#许可与署名) · [相关项目](#相关项目)

</div>

> **非官方项目。** spektrafilm-fnos 是独立的 fnOS（飞牛 NAS）打包与网页前端项目，
> 与 [spektrafilm](https://github.com/andreavolpato/spektrafilm) 及其作者无关、未获背书。
> 上游名称与标识归其权利人所有。

## 这是什么

一个直接以 **spektrafilm 运行时**为引擎的 fnOS 暗房应用：在浏览器里浏览 NAS 上的照片，
选胶片与相纸、调参、渲染、导出。照片留在 NAS 存储池内，应用本地进程直接读写，
导出落回飞牛文件管理器可见的共享目录。

与已有的 [spektralab-fnos](https://github.com/techysy/spektralab-fnos) 的区别在于**上游链**：
那条线跑的是 SpektraLab 的 C++/Vulkan 引擎（SpektraLab 又是 spektrafilm 的重写），
这条线直接跑 spektrafilm 的 Python 运行时——少一层中间项目，许可与署名只对上游负责。

## 状态

**搭建中（v0.1.0，尚未可用）。** 当前仓库只有骨架与合规文件：

- 上游 pin 与许可快照（`UPSTREAM_PIN` + `licenses/`）✓
- 许可与派生关系说明（`NOTICE.md`）✓
- 运行时镜像 / 桥接 / 前端 / fpk 打包 —— 待实现

路线图见 [ROADMAP.md](ROADMAP.md)。**在性能验证通过前不会发布 fpk**：
spektrafilm 是 numba/CPU 运行时，必须先确认 NAS 的 CPU 能撑起可接受的实时预览。

## 架构（计划）

```
浏览器（自研 React/Vite 前端）
   │  HTTP: RPC + SSE 进度 + 文件 API
   ▼
bridge.py（python3 纯标准库，包用户原生运行）
   ├── 进程内调用 spektrafilm 运行时（create_params / simulate / simulate_preview）
   ├── 文件：授权目录浏览 / sidecar / 设置存储 / 上传 inbox
   └── fnOS 模式：trim 开放 API（授权目录 + checkUserACL 逐用户鉴权）
```

与 spektralab-fnos 相比，桥接层不再托管 stdio 二进制帧宿主，而是直接调用上游的
Python API（`spektrafilm.runtime.api` 的 `simulate` / `simulate_preview` / `Simulator`），
少一层进程与协议翻译。HTTP/SSE、文件 API、trim ACL 这些与引擎无关的部分会复用既有实现。

## 计划

1. **性能验证**（一票否决）：NAS 上装 Python 3.13 + spektrafilm 运行时，对真实 RAW 计时。
   若 live 预览 > 数秒，则这条线定位为「单张精修」而非日常暗房。
2. **运行时镜像**：Debian 12 基础 + Python 3.13 venv（`pip install spektrafilm==<pin 版本>`）。
3. **桥接层**：`bridge.py` 复用既有 HTTP/SSE/文件 API/trim ACL，渲染改为进程内调用。
4. **前端**：自研轻量 React/Vite（不复制任何上游 GUI）。
5. **fpk 打包 + CI**：沿用既有 fpk 布局与生命周期脚本经验。

## 快速开始

尚无可用产物。构建与安装说明将在 ROADMAP 第一项完成后补充。

上游运行时本身可按其 [README](https://github.com/andreavolpato/spektrafilm) 安装：

```bash
pip install spektrafilm
```

## 许可与署名

- 本仓库的打包 / 桥接 / 前端代码：**GPL-3.0-or-later**（[LICENSE](LICENSE)）。
- spektrafilm 代码：GPL-3.0-or-later；其胶片/相纸 profile 与印相 LUT：**CC BY-SA 4.0**。
  两份文本的 pin 快照在 [licenses/](licenses/)。
- 按上游 `CITATION.cff` 的要求，本项目的运行时以
  **spectral film modeling powered by `spektrafilm`** 署名，并保留完整引用信息。
- 本项目**不销售任何东西**、不把 LUT/profile 作为付费资产转售、不用于训练商业 AI 模型；
  不冒充官方。详见 [NOTICE.md](NOTICE.md)。

## 相关项目

- [andreavolpato/spektrafilm](https://github.com/andreavolpato/spektrafilm) — 上游：光谱胶片模拟框架（GPL-3.0 + CC BY-SA 4.0）
- [JamesQiu2005/SpektraLab](https://github.com/JamesQiu2005/SpektraLab) — spektrafilm 的 C++/Metal 重写 + 桌面端
- [techysy/spektralab-fnos](https://github.com/techysy/spektralab-fnos) — 同作者的 SpektraLab fnOS 移植（本项目的姊妹线）
