# Mining and Metallurgy Heritage Ontology (MMHO)

**矿冶遗存本体** · v1.6.0

![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)

![Version: 1.6.0](https://img.shields.io/badge/version-1.6.0-blue.svg)

## 简介

Mining and Metallurgy Heritage Ontology（矿冶遗存本体，MMHO）是面向**先秦两汉时期矿冶遗存**的形式化本体，采用 OWL 2 DL 编码，与 CIDOC CRM v7.1.3 国际文化遗产参考本体对齐。本体以"遗址—遗迹—遗物"三层结构组织矿冶考古领域的核心概念，覆盖矿冶活动从原料采集、生产冶炼、产品铸造到废弃处理的完整工艺链条。

## 命名空间与持久化

| 资源                 | URI                               |
| ------------------ | --------------------------------- |
| 本体 IRI             | `http://w3id.org/mmho`            |
| 核心命名空间（前缀 `mmho:`） | `http://w3id.org/mmho#`           |
| 当前版本 IRI（v1.6.x）   | `http://w3id.org/mmho/1.6`        |
| 持久化域名              | `w3id.org`（HTTP 永久重定向）            |
| GitHub 仓库          | <https://github.com/MengJ97/mmho> |

## 当前版本

**v1.6.0**（2026-10-12 发布）

| 指标   | 值                           |
| ---- | --------------------------- |
| 类    | 121（98 mmho + 23 CIDOC CRM） |
| 对象属性 | 62（41 mmho + 21 CIDOC CRM）  |
| 命名实例 | 241                         |
| 三元组  | 2,019                       |

数字均为 `rdflib` 6.x 对当前 `MMHO_v1.6.0_crm_ids.owl` 的实测统计。

## 文件清单

```
mmho/
├── README.md                                        # 本文件
├── LICENSE                                          # CC BY 4.0 许可证
├── CITATION.cff                                     # GitHub 学术引用元数据
├── CHANGELOG.md                                     # 版本变更日志
├── .gitignore                                       # 忽略规则
├── MMHO_v1.6.0_crm_ids.owl                          # 当前版本本体（OWL/XML）
├── MMHO_v1.6.0_crm_ids.ttl                          # 当前版本本体（Turtle）
└── ontology_history/                                # 历史版本存档
    ├── CHANGELOG.md                                 # 历史详细 CHANGELOG 快照
    ├── MMHO_v1.owl                                  # v1.0 初始版本
    ├── MMHO_v1.2.0_bilingual.owl                    # v1.2.0 双语标注版本
    ├── MMHO_v1.2.1_crm_ids.owl                      # v1.2.1 引入 CIDOC CRM 标识
    ├── MMHO_v1.3.0_crm_ids.owl                      # v1.3.0
    ├── MMHO_v1.4.0_crm_ids.owl                      # v1.4.0
    ├── MMHO_v1.4.1_crm_ids.owl                      # v1.4.1
    ├── MMHO_v1.4.4_crm_ids.owl                      # v1.4.4
    ├── MMHO_v1.4.5_crm_ids.owl                      # v1.4.5
    ├── MMHO_v1.4.6_crm_ids.owl                      # v1.4.6
    ├── MMHO_v1.4.7_crm_ids.owl                      # v1.4.7
    ├── MMHO_v1.4.8_crm_ids.owl                      # v1.4.8
    ├── MMHO_v1.4.9_crm_ids.owl                      # v1.4.9
    └──  MMHO_v1.5.0_pre_v15fix_20260907.owl          # v1.5.0 升级前快照

```

## 编码格式

- **主格式**：OWL/XML（适合 Protégé、Reasoner 等工具加载）
- **辅助格式**：Turtle（人工可读，便于版本对比和 diff）
- **DL 子语言**：OWL 2 DL（确保计算完备性和可判定性）
- **可视化**：Protégé 内置 OntoGraf（类层次图、属性关系图）

## 引用方式

**APA 格式**

> 蒋蒙. (2026). *Mining and Metallurgy Heritage Ontology (MMHO) v1.6.0* [Computer software]. <https://github.com/MengJ97/mmho>

**BibTeX** —— 见 [`CITATION.cff`](./CITATION.cff)。GitHub 会自动识别并在仓库页面显示"Cite this repository"按钮。

## 许可证：CC BY 4.0

本作品采用 [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/)（CC BY 4.0）许可。

详细条款见 [`LICENSE`](./LICENSE) 文件。

## 致谢

本体设计参考了以下国际标准与项目：

- [CIDOC CRM v7.1.3](http://www.cidoc-crm.org/) — 国际文化遗产参考本体
- [MIDAS Heritage](https://www.midas-heritage.org.uk/) — 英国文化遗产信息记录
- [SKOS](https://www.w3.org/2004/02/skos/) — 简单知识组织系统

## 联系方式

- **作者**：蒋蒙
- **机构**：北京科技大学 · 科技史与文化遗产研究院
- **GitHub**：<https://github.com/MengJ97>

---

*Last updated: 2026-10-21*
