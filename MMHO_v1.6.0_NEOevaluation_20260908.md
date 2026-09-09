# MMHO v1.6.0 NEOntometrics 结构化质量评估报告（v2 · 修复后）

**评估日期**：2026-09-08 23:56（v2 修复后）
**本体文件**：`MMHO_v1.6.0_crm_ids.owl`（195,834 字节，23:56 写入）
**本体版本**：`owl:versionInfo = 1.6.0`，`dc:date = 2026-09-08`
**评估工具**：ontometrics_owlready.py（基于 owlready2 / OWL API，与 NEOntometrics 平台同源 OntoQA 算法）
**修复历史**：
- 9-08 23:50 蒋蒙手动更新本体（hasVisualFeature/hasCondition Domain 改 mmho:ExcavatedArtifacts，hasDimension Domain 改为遗迹+遗物并集）
- 9-08 23:55 落地9-07 21:35 决策：删除 hasFunctionalSpatialRelation L1934 Class 声明（修复 OWL 不一致——同一 IRI 不可同时是 Property 和 Class）
- 9-08 23:55 同步 owl:versionInfo 1.5.0 → 1.6.0、dc:date 2026-09-03 → 2026-09-08
- 备份：`MMHO_v1.6.0_crm_ids_pre_v16fix2_20260908.owl`

---

## 1. Base Metrics（基础指标）

| 指标 | 数值 | 备注 |
|------|------|------|
| 公理总数（Axioms）| **1,869** | v1.4.6 是 1,822 |
| 逻辑公理数（Logical axioms）| **472** | v1.4.6 是 458 |
| 类数量（Class count，本本体）| **97** | mmho 自定义 |
| 类数量（含 CIDOC CRM）| **120** | 97 + 22 + 其他1 |
| 对象属性数量（Object property，本本体）| **41** | mmho 自定义 |
| 对象属性数量（含 CIDOC CRM）| **61** | 41 + 20 |
| 数据属性数量（Data property）| 0 | |
| 属性总数（Properties）| 41 | |
| 实例数量（Individual）| **230** | NEOntometrics 报告口径 |
| DL Expressivity | **SOIFI** | 与 v1.4.6 一致 |

## 2. Class Axioms（类公理）

| 指标 | 数值 |
|------|------|
| SubClassOf 公理 | 100 |
| Equivalent classes | 0 |
| Disjoint classes | 0 |

## 3. Object Property Axioms（对象属性公理）

| 指标 | 数值 |
|------|------|
| SubObjectPropertyOf | 31 |
| Equivalent object properties | 0 |
| Inverse object properties | 3 |
| Functional | 5 |
| Inverse functional | 0 |
| Transitive | 3 |
| Symmetric | 1 |
| Asymmetric | 1 |
| Reflexive | 0 |
| Irreflexive | 1 |
| **Domain axioms** | **52**（v1 修复前是 50，+2 来自 hasVisualFeature / hasCondition 各加 mmho:ExcavatedArtifacts Domain）|
| Range axioms | 41 |

## 4. Individual Axioms（实例公理）

| 指标 | 数值 |
|------|------|
| Class assertion axioms（类声明公理）| 234 |

## 5. Schema Metrics（模式指标，OntoQA 算法）

| 指标 | 数值 | 含义 |
|------|------|------|
| 属性丰富度（Attribute richness）| 0.000000 | 数据属性=0 |
| 继承丰富度（Inheritance richness）| 1.030928 | 类平均子类数 |
| 关系丰富度（Relationship richness）| 0.290780 | 非继承关系占比 |
| 公理/类比（Axiom/class ratio）| **19.268041** | 平均每类公理数（v1 是 19.247）|
| 类/关系比（Class/relation ratio）| 2.365854 | 类数与非继承关系数之比 |

## 6. Graph Metrics（图结构指标）

| 指标 | 数值 | 含义 |
|------|------|------|
| 绝对根节点数 | 25 | |
| 绝对叶节点数 | 76 | |
| 绝对深度 | 244 | |
| 最大深度 | 5 | |
| 平均深度 | 2.52 | |
| 最大宽度 | 7 | |
| 绝对宽度 | 72 | |
| 平均宽度 | 0.74 | |
| 叶节点扇出率 | 1.000000 | |
| 缠绕度 | 0.000000 | 无多继承 |

---

## 7. 修复前后对比（v1 23:11 vs v2 23:56）

| 指标 | v1（23:11 假数据）| **v2（23:56 真实数据）** | 差异 |
|------|--------|--------|------|
| 公理总数 | 1,867 | **1,869** | +2 |
| 逻辑公理 | 470 | **472** | +2 |
| **Domain axioms** | 50 | **52** | +2 |
| 公理/类比 | 19.247 | **19.268** | +0.021 |
| 其他（Class / OP / NI / DL / SubClassOf / SubObjectPropertyOf / Functional / Transitive 等）| 同 v2 | 同 v2 | 不变 |

**关键**：v1 报告的 SOIFI 是基于"OWL 宽松加载"的结果（同一 IRI 既作 Property 又作 Class 时 owlready2 有时不会立刻报错）。v2 修复 L1934 Class 声明后，DL Expressivity 仍为 SOIFI——说明 Inverse functional 性质来自其他属性，删除 hasFunctionalSpatialRelation Class 声明不影响 DL Expressivity。

---

## 8. JSON 报告原文

文件：`D:\电脑26.6版\博士相关\先秦两汉矿冶遗存知识图谱课题\知识组织构建\ontology\MMHO_v1.6.0_crm_ids_ontometrics.json`

---

## 9. 实例数 230 vs OWL XML 元素数 236 口径说明

MMHO 本体中存在 6 个**金属材料枚举 NI**（Bronze / Copper / Iron / Lead / Steel / Tin），其定义采用 `owl:OneOf` 枚举表达（在 mmho:MetallicMaterials 类的 EquivalentClass 中），未单独声明 `rdf:type` 关系。NEOntometrics 按 Class assertion axioms 口径统计的实例数为 230。本报告所有实例数量引用 NEOntometrics 口径（230）。

---

*报告生成：2026-09-08 23:56*