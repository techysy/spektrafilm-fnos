# ROADMAP

spektrafilm-fnos 的状态：**搭建中**。本文件是公开的进度与决策记录。

## 第 0 步：性能验证（进行中，一票否决）

spektrafilm 是 numba/CPU 运行时，没有 GPU 路径。这条线是否值得做，取决于 NAS 的 CPU
能否撑起可接受的实时预览。

- [ ] 在 NAS 上装 Python 3.13 + spektrafilm 运行时（`pip install spektrafilm`）
- [ ] 用真实 RAW（24MP 级别）跑 `simulate_preview` / `simulate` 计时
- [ ] 记录：live（~1600px）、preview（~3400px）、full（全尺寸）三档耗时
- [ ] 判定：live 在数秒内 → 继续；否则把定位改为「单张精修」，并重新评估是否值得发 fpk

> 参考：SpektraLab 的 C++/Vulkan 引擎在 Intel UHD 730 上 full 渲染 ~4.6s。
> CPU 运行时大概率慢一个数量级——这正是要先量的原因。

## 第 1 步：运行时镜像

- [ ] Dockerfile：Debian 12 基础 + Python 3.13（fnOS 自带 3.11，运行时需自带 venv）
- [ ] `pip install spektrafilm==<pin 版本>`，锁定依赖
- [ ] 冒烟：容器内 `from spektrafilm import simulate` 跑通一张测试图
- [ ] 生成 `licenses/THIRD_PARTY.md`（pip 依赖的许可清单）

## 第 2 步：桥接层

- [ ] 复用 spektralab-fnos 的 `bridge.py`：HTTP/SSE、文件 API、上传 inbox、路径夹紧
- [ ] 复用 `fnos_trim.py`：trim 开放 API + `checkUserACL` 逐用户鉴权
- [ ] 渲染路径改为进程内调用 spektrafilm 运行时（不再托管 stdio 宿主）
- [ ] 冒烟：hello / open / render / export 全链路

## 第 3 步：前端

- [ ] 自研 React/Vite 轻量界面（不复制任何上游 GUI）
- [ ] 子路径适配（fnOS 统一网关 `/app/<appname>/`）
- [ ] 渲染进度接入 SSE

## 第 4 步：fpk 打包 + CI

- [ ] fpk 布局、生命周期脚本、权限（沿用既有经验）
- [ ] CI：镜像构建 + 冒烟 + fpk staging
- [ ] tag `v*` 自动发 Release

## 已决策

- **不复制上游 GUI**：spektrafilm 的 GUI 是 Qt + napari 桌面应用，不适合网页；自研前端。
- **不自动跟随上游**：`UPSTREAM_PIN` 钉死，升级人工过一遍许可与 API 变更。
- **不发未完成产物**：性能验证通过前不发布 fpk。
- **合规优先**：`licenses/` 保留上游许可快照，`NOTICE.md` 写明派生关系与署名。

## 风险

- CPU 性能可能不达标（第 0 步即验证）
- `requires-python ~=3.13` 与 fnOS 的 Debian 12（3.11）错位，需自带 Python
- 依赖体积大（numpy/scipy/numba/pyfftw/rawpy/…），镜像可能 200MB+
- 上游 `CITATION.cff` 对「受方法启发也算衍生」的措辞偏紧，公开分发前建议向上游作者说明
