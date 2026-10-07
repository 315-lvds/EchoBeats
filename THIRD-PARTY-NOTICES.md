# 第三方依赖声明（Third-Party Notices）

> **版本**：V1.0.0 ｜ **建立**：2026-10-04 ｜ **依据**：许可证整改（见 `docs/design/许可证与依赖整改-设计.md`）
> **口径**：本文件的依赖表是**从产线导入面实测反推**的（AST 扫 `engine/sonic/**` ＋ `importlib.metadata` 读版本与许可证），
> ⛔ 不是凭印象罗列。复现脚本 = `engine/tests/gen_notices.py`。

## 一、本软件自身的许可

| 项 | 值 |
|---|---|
| 本软件 | **EchoBeats**（引擎 `engine/sonic/` ＋ 客户端 `app/`）|
| 自身许可 | **PolyForm Noncommercial License 1.0.0**（SPDX：`PolyForm-Noncommercial-1.0.0`）—— 版权归 **EchoBeats Project**（全文 `LICENSE` · 中文说明 `LICENSE-中文说明.md`）|
| 姿态（**2026-10-07 定案**）| ⭐ **非商业用途免费**：许可原文把"任何非商业目的"列为许可用途（个人研究/实验/为公共知识测试/学习/私人娱乐/爱好项目 ＋ 慈善、教育、公共研究、公共安全卫生、环保、政府机构 —— 不论经费来源）；✅ **允许二次修改与再分发**，须带 `LICENSE` 或官方网址 ＋ `Required Notice:` 那行 ＋ ⛔ 不得暗示原作者背书；⛔ **商业用途未获授权** ⇒ 通过本仓库 Issue 联系作者；⚠️ **它不是 OSI 认可的开源许可**（因限制商业用途）⇒ 对外应称 **source-available（源码可见）**，⛔ 不要称"开源"；⛔ 第三方组件**不受**本声明约束 |
| ⚠️ **为什么是"非商业"而不是 MIT / AGPL**（**决策依据，⛔ 别当成随手定的**）| ⭐ 包内**装着训自 MUSDB 族（CC BY-NC-SA）的权重**（De-limiter / 分离器，见 §五）⇒ **用 MIT 发布等于代替上游授予一项自己可能没有的商用权**，而 MIT 的免责条款**免的是担保责任、免不了"无权授权"这件事** ⇒ 风险落在作者自己身上。**AGPL 在桌面产品上牙齿钝**（其杀手条款长在网络服务；桌面程序被约束时，合规路径只是"把源码一起发出去"，对改名转卖者很便宜）⇒ 收不到钱、也拦不住人。⇒ ⭐ **"非商业"与当前发行物自洽**：NC 权重 ⇄ NC 项目。**若将来要放开商用，必须先**把两个权重换成**自造损伤重训**的版本（§四·附已给路线），**再**考虑 AGPL ＋ 商业授权双许可（⚠️ 且须在收第一个外部贡献**之前**立 CLA/DCO，否则永久失去双许可能力）|

## 二、产线依赖（✅ 全部宽松许可 ⇒ 可商用）

| 发行包 | 版本 | 许可证 | 义务 |
|---|---|---|---|
| `librosa` | 0.11.0 | **ISC** | 保留版权与许可声明 |
| `scipy` | 1.17.1 | **BSD-3-Clause** | 同上 |
| `numpy` | 2.4.6 | **BSD-3-Clause**（并含 0BSD/MIT/Zlib 等子许可）| 同上 |
| `torch` | 2.6.0+cu124 | **BSD-3-Clause** | 同上 |
| `soundfile` | 0.13.1 | **BSD-3-Clause**（Python 包装层）| 同上 ＋ ⚠️ **见「三」的 libsndfile** |
| `mosqito` | 1.2.1 | **Apache-2.0** | 保留声明 ＋ NOTICE |
| `torch-log-wmse` | 1.1.0 | **Apache-2.0** | 同上 |
| `pyloudnorm` | 0.2.0 | **MIT** | 保留声明 |
| `mir_eval` | 0.8.2 | **MIT** | 同上 |
| `miniaudio` | 1.71 | **MIT** | 同上 |
| `matplotlib` | 3.10.8 | **PSF 类** | 同上 |

## 三、⚠️ 传递依赖中的**弱传染（LGPL）**—— 随分发必须满足

| 组件 | 版本/来源 | 许可证 | 我们的义务（LGPL-2.1+）|
|---|---|---|---|
| **libsndfile** | 由 `soundfile` 的 wheel **捆绑**（独立 DLL）| **LGPL-2.1+** | ① 保留声明 ② **允许用户替换该库**（独立 DLL ⇒ ✅ 满足）③ 若修改其源码须回馈（⛔ 我们未修改）|
| `soxr` | `librosa` 的依赖 | **LGPL-2.1** | 同上（独立扩展模块）|

⇒ ✅ **LGPL 允许商用与闭源**，只要满足"**动态链接 ＋ 可替换**"；我们**不静态链接**这些库。

## 四、⛔ 已移除的传染性依赖（本轮的整改主体）

| 组件 | 原状态 | 问题 | 处置 |
|---|---|---|---|
| **`mutagen`** | `scanner.py`（读）＋ `metaio.py`（写）**同进程 import** | dist-info 写着 **`License-Expression: GPL-2.0-or-later`**（源码头同）⇒ **把引擎拖进 GPL** | ✅ **自实现** `engine/sonic/tagio.py`（FLAC Vorbis comment＋PICTURE；MP3 ID3v2.3＋APIC）⇒ 读写都换掉 |
| **内置 `tools/ffmpeg/ffmpeg.exe`** | 87.6 MB · `--enable-gpl --enable-version3` ＋ **静态 x264/x265** | ① **GPLv3 分发义务** ② 牵 **x264/x265/AAC 专利池**（Via LA 2026 仍在收费；官方 FAQ 明文「**encoder and/or decoder** 均需许可」）| ✅ **整目录删除** ＋ **格式白名单**（只 `FLAC/MP3/OGG/Opus/WAV/AIFF`；⛔ 拒 `m4a/aac/wma/mp4`）；响度时间线改自实现回退（口径 LUFS→dBFS-RMS，已登记）|
| **`pedalboard`** | 在**产线包目录** `engine/sonic/data/degradation.py` 内**懒 import** | 许可证 = **GPL** ⇒ 审计上不干净（打包工具可能扫进去）| ✅ **`git mv` 到 `engine/tests/degradation.py`**（工具链，⛔ 不进分发物）⇒ 产线包已无 GPL 代码 |
| **`SonicMaster`** | 权重/脚本在 `models/`，已在 pipeline 注册 | 许可证**待核**（HF `amaai-lab/SonicMaster`；数据集 tag 可疑）＋ **接线本就坏**（永远 FAILED 但 `is_available()` 报 True）| ✅ **整体移除**（代码 5 处 ＋ 删 `models/sonicmaster*` 全部含权重）；⭐ 恢复线索 = HF `amaai-lab/SonicMaster` |

## 四·附 ⭐ **命名澄清：`declip`（曾写作「DL-1」）不是 DL**

| 命令 | 真实实现 | 含模型/权重？ | 依赖训练数据？ |
|---|---|---|---|
| **`declip`（去削波）** | **加权 ℓ1 + Douglas–Rachford**（凸优化）＋ Gabor 紧框架 ／ 另两条实现为三凸集 POCS 与 Chambolle–Pock | ⛔ **无**（已实测：该段代码里 `torch`/`models/`/`checkpoint`/`.pth` 出现 **0 次**）| ⛔ **无** ⇒ ⭐ **许可上完全干净、可商用** |
| `loudness`（响度修复）| 增益 + 真峰限幅 | ⛔ 无 | ⛔ 无 ⇒ ✅ 干净 |
| `delimiter`（De-limiter）| **外部仓库 `models/delimiter`，权重 9.4 MB 在盘** | ✅ **有** | ⚠️ **训练数据含 Musdb-XL / MUSDB 族** ⇒ 灰色地带 |
| 分离（`bs_roformer` / `SCNet`）| 外部权重 | ✅ 有 | ⚠️ 训练数据 MUSDB ⇒ 灰色地带（且服务的是 **remix 线**，非修复主线）|

⚠️ **为什么必须写这条**：历史文档/命令名把去削波写作「**DL-1**」，**这个误称会误导审计**
（2026-10-04 实测确实误导过一次判断：以为"DL 不能用 ⇒ 整个修复链白做"）。⇒ 已订正其 docstring。
⭐ **结论**：本项目**两个真正可用的修复功能（响度修复 / 去削波）都是自研 DSP，⛔ 不吃数据许可**；
灰色只落在 **De-limiter 与分离器**两处（建议路线 = **用自造损伤对训自己的权重**）。

## 五、⚠️ 数据与模型的许可边界（**不是**代码许可，但必须声明）

| 语料/权重 | 许可状态 | 我们的用法边界 |
|---|---|---|
| **MUSDB18 / MUSDB18-HQ** | **CC BY-NC-SA 4.0 族**（⛔ 非商用）| ⭐ **只用于内部评测与公开报告**；⛔ **不再作为产品内阈值的来源**（`reference_stats.json` 的来源已改为**自造成对样本**，数值保留但**不再声称派生自它**）|
| **MSR Bench / MSR 2025 testset** | 实测 README 原文 = **`license: cc-by-nc-4.0`** | 同上（仅内部评测）|
| **`bakeoff_sota`** | 许可冲突（Zenodo API `cc-by-4.0` vs README `CC BY-NC-SA 4.0`）| **取严**（按 NC 处理）|
| **DL 权重**（De-limiter / BS-Roformer / SCNet）| ⚠️ **训练数据含 MUSDB18 族**（业界公认灰色地带）| ⚠️ **专项待办**：商用前须评估，或换用可商用数据训练的权重 |

## 六、工具链依赖（⛔ **不进分发物**，但在此登记以便审计）

| 组件 | 用途 | 许可证 |
|---|---|---|
| `pedalboard` | **降级语料生成**（`engine/tests/degradation.py`）| **GPL** ⛔（仅在测试侧使用）|
| `mutagen` | **测试用独立仲裁者**（回读验证自实现的写入，T5 验收）| **GPL-2.0+** ⛔（⛔ 产品代码已无引用）|
| `soxr` / `lameenc` | 音频重采样 / MP3 编码（`demucs` 链）| LGPL-2.1 / LGPL-3.0 |

⚠️ **打包纪律**：产出分发物时必须**确认上述工具链依赖未被 bundle**（PyInstaller 的自动分析通常只收可达模块；
本轮的 `git mv` 正是为了让 `pedalboard` **不在产线包内**）。

## 七、免责

⚠️ 本文件是**工程层面的许可梳理，不是法律意见**。专利与数据许可的最终判断建议咨询专业律师，
待问清单见 `docs/design/许可证与依赖整改-设计.md` 与审计报告（`.agent_bus/lic_engine_and_patents.md` ·
`.agent_bus/lic_data_models.md`）。

## 修订记录

| 版本 | 日期 | 改动 | 原因 |
|---|---|---|---|
| **V1.1.0** | **2026-10-07** | ⭐ **自身许可定案 = PolyForm Noncommercial License 1.0.0**（取代"许可证未定"的自定义声明）＋ 记下**决策依据**（为什么不选 MIT／AGPL：NC 派生权重 ＋ 桌面产品上 AGPL 牙齿钝）＋ 名称与图标**不随代码许可授权** ＋ 复扫确认 `site-packages` **无 GPL/AGPL/SSPL/BUSL 命中**、**未捆 ffmpeg/avcodec/x264** | 用户裁定；公开仓库前的许可与口径收口（0.7.3）|
| **V1.0.0** | **2026-10-04** | 建立本文件：自身许可（当时定 Apache-2.0，**后被 2026-10-06 用户裁定取代**）· 产线依赖 11 项（全宽松）· LGPL 传递依赖义务（libsndfile/soxr）· **已移除的四个传染性依赖**（mutagen / ffmpeg / pedalboard / SonicMaster）· **数据与模型的许可边界**（MUSDB18/MSR 限内部评测）· 工具链登记与打包纪律 | 许可证整改（T8）；用户令「**我需要知道我们目前做的这些东西到底是开源可商用，还是开源不可商用，还是必须公开**」|
