# Mining and Metallurgy Heritage Ontology (MMHO)

**矿冶遗存本体** · v1.6.0

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Version: 1.6.0](https://img.shields.io/badge/version-1.6.0-blue.svg)]()
[![DOI: pending](https://img.shields.io/badge/DOI-pending-yellow.svg)]()

## 简介

Mining and Metallurgy Heritage Ontology（矿冶遗存本体，MMHO）是面向**先秦两汉时期矿冶遗存**的形式化本体，采用 OWL 2 DL 编码，与 CIDOC CRM v7.1.3 国际文化遗产参考本体对齐。本体以"遗址—遗迹—遗物"三层结构组织矿冶考古领域的核心概念，覆盖矿冶活动从原料采集、生产冶炼、产品铸造到废弃处理的完整工艺链条。

本体支持矿冶遗址综合调查数据的知识组织、语义查询、跨案例推理，是论文《矿冶遗存本体构建与知识表达研究》的核心成果之一。

## 命名空间与持久化

| 资源 | URI |
|------|-----|
| 本体 IRI | `http://w3id.org/mmho` |
| 核心命名空间（前缀 `mmho:`） | `http://w3id.org/mmho#` |
| 当前版本 IRI（v1.6.x） | `http://w3id.org/mmho/1.6` |
| 持久化域名 | `w3id.org`（HTTP 永久重定向） |
| GitHub 仓库 | <https://github.com/MengJ97/mmho> |

> **注**：w3id.org 永久 URI 当前正在社区审核流程中（PR 提交至 `perma-id/w3id.org`）。审核通过后 `http://w3id.org/mmho` 将通过 HTTP 重定向解析至本 GitHub 仓库对应的本体文件。

## 当前版本

**v1.6.0**（2026-09-08 发布）

| 指标 | 值 |
|------|-----|
| 公理总数 | 1,869 |
| 逻辑公理 | 472 |
| 类数量（本本体 / 含 CRM）| 97 / 120 |
| 对象属性（本本体 / 含 CRM）| 41 / 61 |
| 实例数量 | 230 |
| DL Expressivity | SOIFI |
| 评估工具 | NEOntometrics |

详细评估见 [`MMHO_v1.6.0_NEOevaluation_20260908.md`](./MMHO_v1.6.0_NEOevaluation_20260908.md)。

## 文件清单

```
mmho/
├── README.md                                          # 本文件
├── LICENSE                          # CC BY 4.0 许可证全文
├── CITATION.cff                    # GitHub 学术引用元数据
├── CHANGELOG.md                    # 版本历史
├── MMHO_v1.6.0_crm_ids.owl        # 当前版本本体（OWL/XML 格式）
├── MMHO_v1.6.0_crm_ids.ttl        # 当前版本本体（Turtle 格式）
├── MMHO_v1.6.0_crm_ids_ontometrics.json  # NEOntometrics 评估报告（JSON）
├── MMHO_v1.6.0_NEOevaluation_20260908.md # NEOntometrics 评估报告（Markdown）
└── ontology_history/               # 历史版本存档
    ├── MMHO_v1.owl                # 初始版本
    ├── MMHO_v1.2.0_bilingual.owl  # 双语标注版本
    ├── MMHO_v1.2.1_crm_ids.owl
    ├── MMHO_v1.3.0_crm_ids.owl
    ├── MMHO_v1.4.0_crm_ids.owl
    ├── MMHO_v1.4.1_crm_ids.owl
    ├── MMHO_v1.4.4_crm_ids.owl
    ├── MMHO_v1.4.5_crm_ids.owl
    ├── MMHO_v1.4.6_crm_ids.owl
    ├── MMHO_v1.4.7_crm_ids.owl
    ├── MMHO_v1.4.8_crm_ids.owl
    ├── MMHO_v1.4.9_crm_ids.owl
    ├── MMHO_v1.5.0_pre_v15fix_20260907.owl
    ├── MMHO_v1.6.0_pre_v15fix2_20260907.owl
    ├── MMHO_v1.6.0_crm_ids_pre_v16fix2_20260908.owl
    └── （其他备份 / 修复前版本）
```

## 编码格式

- **主格式**：OWL/XML（标准格式，适合 Protégé、Reasoner 等工具加载）
- **辅助格式**：Turtle（人工可读，便于版本对比和 diff）
- **DL 子语言**：OWL 2 DL（确保计算完备性和可判定性）
- **可视化**：Protégé 内置 OntoGraf（类层次图、属性关系图）

## 引用方式

如果本研究对您的研究有帮助，请按以下方式引用：

**APA 格式**：
> 蒋蒙. (2026). *Mining and Metallurgy Heritage Ontology (MMHO) v1.6.0* [Computer software]. https://github.com/MengJ97/mmho

**BibTeX 格式**（请参考 [`CITATION.cff`](./CITATION.cff)）：
```bibtex
@software{mmho2026,
  author = {蒋蒙},
  title = {Mining and Metallurgy Heritage Ontology (MMHO)},
  version = {1.6.0},
  year = {2026},
  url = {https://github.com/MengJ97/mmho},
  license = {CC-BY-4.0}
}
```

GitHub 会在仓库页面自动识别 `CITATION.cff` 并显示"Cite this repository"按钮。

## 关联论文

> 蒋蒙. 《矿冶遗存本体构建与知识表达研究》[D]. 北京科技大学科技史与文化遗产研究院, 2026.

本体的详细设计、类层次结构、关系建模、实例化方法可参考该论文第三、四、五章。

## 许可证**：CC BY 4.0

本作品采用 [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/)（CC BY 4.0）许可。

**您可以自由地**：
- **共享** — 复制、转载本作品
- **改编** — 基于本作品进行二次创作（包括商业用途）

**但须遵守以下条件**：
- **署名** — 您必须给出适当的署名，提供指向本许可协议的链接，并标明是否对原始作品作了修改

详细条款见 [`LICENSE`](./LICENSE) 文件。

## 致谢

本体设计参考了以下国际标准与项目：
- [CIDOC CRM v7.1.3](http://www.cidoc-crm.org/) — 国际文化遗产参考本体
- [MIDAS Heritage](https://www.midas-heritage.org.uk/) — 英国文化遗产信息记录
- [SKOS](https://www.w3.org/2004/02/skos/) — 简单知识组织系统
- [NEOntometrics](https://github.com/ISE-FIZKarlsruhe/neonometrics) — 本体质量评估工具

## 联系方式

- **作者**：蒋蒙
- **机构**：北京科技大学 · 科技史与文化遗产研究院
- **GitHub**：<https://github.com/MengJ97>

---

*Last updated: 2026-09-09*