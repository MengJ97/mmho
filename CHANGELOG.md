# MMHO Changelog

**Mining and Metallurgy Heritage Ontology** · `mmho:`

本体 IRI：`http://w3id.org/mmho`  
配套基础：`CIDOC CRM v7.1.3` + `CRMarchaeo v2.1`

本日志记录每个版本的关键技术决策与本体元素计数变化。所有描述面向公开发布，不含个人对话、修订过程或未公开论文引用。

---

## 版本时间线

| 版本    | 日期         | 核心改动                                                                                                                      |
| ----- | ---------- | ------------------------------------------------------------------------------------------------------------------------- |
| 1.2.0 | 2026-06-14 | 双语标注：`Class`/`ObjectProperty`/`NamedIndividual` 全量补 zh + en label                                                         |
| 1.2.1 | 2026-06-14 | 引入 CIDOC CRM 实体 IRI（`crm:` 前缀标准化）                                                                                         |
| 1.3.0 | 2026-07-14 | 新增 3 类关系：`hasPredecessor`/`hasSuccessor`（互逆对）、`hasFunctionalSpatialRelation`、`hasProcessEvolution`                        |
| 1.4.0 | 2026-07-14 | 新增 `hasFunctionalSpatialRelationType`（FSRT 作为 `Class` + `subClassOf crm:E55_Type`）                                        |
| 1.4.3 | 2026-07-17 | FSRT 类型修正：`<owl:NamedIndividual>` 改 `<owl:Class>`，父类从直接 `crm:E55_Type` 改为 `mmho:Type` 间接继承；删除 4 个硬编码枚举实例                  |
| 1.4.4 | 2026-07-17 | 补 7 个缺 label 的 Class；`wasProducedAt` Range 类型不匹配修正；朝代按国际学术惯例重命名                                                           |
| 1.4.5 | 2026-07-18 | 解决 `hasPredecessor`/`hasSuccessor` 与 `hasAssociationWith` 的 `P69` 形式冲突；`belongsToPeriod` 移除 `Functional`；补两个 `Functional` |
| 1.4.6 | 2026-07-22 | 朝代从 Class 改为 NamedIndividual（统一指向 `mmho:Period`）；新增 `mmho:hasBroaderPeriod`；器物组合件建模；二里岗映射修正；动物纹饰命名统一                      |
| 1.4.7 | 2026-08-14 | 新增首个 `DatatypeProperty` × 2：`mmho:hasArea` / `mmho:hasConservationLevel`                                                  |
| 1.4.8 | 2026-08-24 | `mmho:hasDimension` 的 `Domain` 扩展为 `unionOf(ExcavatedArtifacts, AnthropicFeature)`                                        |
| 1.4.9 | 2026-08-27 | 内部细节调整                                                                                                                    |
| 1.5.0 | 2026-09-03 | `mmho:hasVisualFeature` 与 `mmho:hasCondition` 的 Domain 从 `mmho:SmeltingProducts` 扩展到 `crm:E18_Physical_Thing`             |
| 1.6.0 | 2026-10-12 | `mmho:Type` 子类层级集中化（包括 `mmho:ConservationLevel` 等通过 `mmho:Type` 间接继承 `crm:E55_Type`）；同步 `OWL/XML` 与 `Turtle` 两种序列化        |

---

## v1.4.4 详细改动

### 1. 补 7 个缺 label 的 Class

`v1.2.0` 时代遗留的"基本元素类"补中英文 label：`Bronze`（青铜）/ `Copper`（纯铜）/ `Iron`（铁）/ `Steel`（钢）/ `Lead`（铅）/ `Tin`（锡）/ `Cupellation`（灰吹法）。

### 2. 错别字修正

`mmho:GeographicCoordinates` 的英文 label 由 `Geographic Cordinates` 改为 `Geographic Coordinates`。

### 3. 朝代命名按国际学术惯例

为避免与国际上同名朝代冲突：`mmho:JinDynasty`（晋代 266-420）拆为 `WesternJin`（西晋 266-316）与 `EasternJin`（东晋 317-420）；`mmho:JurchenJin`（金代 1115-1234）单独顶级命名以避免"两个 Jin"的英文混淆。命名依据为西方考古学界惯例：使用 `Western Jin`/`Eastern Jin` 与 `Jurchen Jin` 区分两段同名朝代。

### 4. `mmho:wasProducedAt` Range 类型不匹配修正

原 `wasProducedAt` 误挂 `crm:P53_has_former_or_current_location`（Range = `crm:E53_Place`），但实际 Range 为 `mmho:InferredPlaceOfOrigin`（`crm:E13_Attribute_Assignment` 子类，建模"推断"行为）。Range 类型与父属性不兼容。

**修复**：解除父属性，`wasProducedAt` 作为独立 mmho 属性保留 Domain `mmho:ExcavatedArtifacts` → Range `mmho:InferredPlaceOfOrigin`。设计与 `mmho:hasFunctionalSpatialRelation`/`mmho:hasProcessEvolution` 一致。

---

## v1.4.5 详细改动

### 1. 解决 `hasPredecessor`/`hasSuccessor` 与 `hasAssociationWith` 的 `P69` 形式冲突

`mmho:hasAssociationWith` 是 `crm:P69_has_association_with` 的子属性，且设为 `Symmetric`。`mmho:hasPredecessor`/`mmho:hasSuccessor` 若同挂 `crm:P69`，形式上与对称属性"平级"，会与工序的有向语义产生混淆。

**修复**：

- 删除 `hasPredecessor`/`hasSuccessor` 的 `<rdfs:subPropertyOf crm:P69_has_association_with/>`，两属性均改为独立属性
- `mmho:hasSuccessor` 加 `<rdf:type owl:AsymmetricProperty/>`，明确有向性
- `hasPredecessor` 通过 `InverseOf` 自动推理为 `Asymmetric`（无需显式声明）

修复后 `crm:P69` 子属性链仅剩 `hasAssociationWith`，`hasPredecessor`/`hasSuccessor` 与之形式上分离。

### 2. `mmho:belongsToPeriod` 移除 `Functional`

矿冶遗址常跨朝代连续作业（如商代开始到汉代结束），强 `Functional` 会强制每对象最多一个时期，丢失多时期实例。

**修复**：移除 `belongsToPeriod` 的 `Functional` 声明。Domain 三个类（`mmho:AnthropicFeature`/`mmho:ExcavatedArtifacts`/`mmho:MiningAndMetallurgySite`）保持不变。修改后每对象可挂多个 `mmho:Period` 实例。

### 3. 补两个 `Functional`

按"单值属性"建模守则补全两条属性的 `Functional` 声明：

| 属性                             | 父属性                                      | Domain                    | Range                        |
| ------------------------------ | ---------------------------------------- | ------------------------- | ---------------------------- |
| `mmho:identifiedByCoordinates` | `crm:P168_place_is_defined_by`           | `mmho:Location`           | `mmho:GeographicCoordinates` |
| `mmho:wasFoundAt`              | `crm:P53_has_former_or_current_location` | `mmho:ExcavatedArtifacts` | `mmho:Location`              |

每 `Location` 一组坐标、每件器物一个出土地 —— 语义为"单值"。

### 4. 不挂父属性的 mmho ObjectProperty（v1.4.5 最终：8 个）

| ObjectProperty                          | 不挂父属性的理由                                                                 |
| --------------------------------------- | ------------------------------------------------------------------------ |
| `mmho:conductedAtSite`                  | mmho 特有语义（"活动在某遗址进行"），CIDOC CRM 无对应 P                                    |
| `mmho:excavatedFrom`                    | mmho 特有语义（"遗物来自哪个遗迹单元"）                                                  |
| `mmho:hasFunctionalSpatialRelation`     | mmho 特有语义（功能驱动的空间关系）                                                     |
| `mmho:hasFunctionalSpatialRelationType` | `CRMarchaeo v2.1 AP11.1 has type` 对应物（punning Domain）                    |
| `mmho:hasPredecessor`                   | v1.4.5 新增独立：避免与 `hasAssociationWith (Symmetric)` 形式平级                    |
| `mmho:hasProcessEvolution`              | 同 `hasPredecessor`，避免与对称属性形式冲突                                           |
| `mmho:hasSuccessor`                     | 同 `hasPredecessor`                                                       |
| `mmho:wasProducedAt`                    | Range 类型不匹配（`P53 Range=E53_Place ≠ mmho Range=E13_Attribute_Assignment`） |

### 5. 本体元素计数变化

| 类型              | v1.4.4 | v1.4.5 | 变化                 |
| --------------- | ------ | ------ | ------------------ |
| Class           | 134    | 134    | 0                  |
| NamedIndividual | 223    | 223    | 0                  |
| ObjectProperty  | 57     | 57     | 0（删除 2 行 + 新增 2 行） |

---

## v1.4.6 详细改动

### 1. 朝代建模：Class 改为 NamedIndividual

`v1.4.5` 及更早版本将 18 个朝代（夏/商/周/秦/汉/三国/晋/南北朝/隋/唐/五代十国/宋/辽/金/西夏/元/明/清）建模为顶层 Class，与"朝代作为可枚举时段"的语义不符。

**修复**：

- 删除 18 个朝代顶级 Class
- 全部朝代改为 `mmho:Period` 的 NamedIndividual（`mmho:Period` 父类保持 `crm:E41_Appellation`，建模"朝代名 = 称谓/标识"）
- 新增 19 个主朝代 + 13 个朝代细分 NamedIndividual（如 `WesternJin`/`EasternJin`、`EarlyShang`/`LateShang`、`NorthernSong`/`SouthernSong` 等）

### 2. 新增 `mmho:hasBroaderPeriod`

- `Domain` = `Range` = `mmho:Period`
- `Transitive`，方向：子 → 父
- 不挂 `crm:P127_has_broader_term`（避免 `mmho:Period ≠ crm:E55_Type` 类型冲突）

29 条层级关系按附录时代树建立（如 `WesternJin hasBroaderPeriod JinDynasty`、`ErligangLower hasBroaderPeriod EarlyShang`）。

### 3. 器物组合件（`mmho:ArtifactAssemblage`）

新增 `Class` `mmho:ArtifactAssemblage`（`subClassOf mmho:FinalProducts`），配合 `ObjectProperty` `mmho:consistsOfComponent`（`Domain=ArtifactAssemblage`，`Range=FinalProducts`，`subPropertyOf crm:P46i_forms_part_of`）。

具体某一组（如"曾侯乙编钟组合"）在 ABox 数据填充时建模为 `mmho:ArtifactAssemblage` 的实例；TBox 领域本体仅定义类型概念，不预设具体组合件实例。

### 4. 二里岗映射修正

原 `ErligangLower → MiddleShang`、`ErligangUpper → LateShang`（错位）。按国际考古分期修正为：

- `ErligangLower`（二里岗下层）→ `EarlyShang`（商代早期）
- `ErligangUpper`（二里岗上层）→ `MiddleShang`（商代中期）

### 5. 动物纹饰命名统一

5 个 `AnimalMotifPattern` NI 中英文 label 统一加"纹"/"Motif"：鸮 → 鸮纹（Owl Motif）/ 猪 → 猪纹（Pig Motif）/ 象 → 象纹（Elephant Motif）/ 犀 → 犀纹（Rhinoceros Motif）/ 虎 → 虎纹（Tiger Motif）。与父类 `AnimalMotifPattern` 命名风格一致。

### 6. 其他清理

- 删除 `hasBroaderTerm` 升级中遗留的 2 个匿名 Class
- 删除 2 个孤儿 Province NI（`Jiangxi_Province`/`Shanxi_Province`，无 `rdf.type`）
- 删除 `Xiao` 多余 zh label "鸮"（与 v1.4.6 新加的"鸮纹"重复）

### 7. 本体元素计数变化

| 类型                    | v1.4.5 | v1.4.6 | 变化                                    |
| --------------------- | ------ | ------ | ------------------------------------- |
| Class（含 CRM）          | 134    | 116    | -18（删 18 朝代 + 增 1 ArtifactAssemblage） |
| ObjectProperty（含 CRM） | 57     | 58     | +1（`consistsOfComponent`）             |
| NamedIndividual       | 223    | 226    | +3                                    |
| 总公理                   | 1844   | 1822   | -22                                   |
| 逻辑公理                  | 460    | 458    | -2                                    |
| `mmho:Period` 实例      | 0      | 47     | +47                                   |

---

## v1.4.7 详细改动

### 1. 新增 `mmho:hasArea`（遗址面积）

- `DatatypeProperty`（mmho 本体首个 `DatatypeProperty`）
- `Domain` = `mmho:MiningAndMetallurgySite`
- `Range` = `xsd:decimal`
- comment (zh)：遗址面积，数值记录，单位平方公里

### 2. 新增 `mmho:hasConservationLevel`（保护级别）

- `DatatypeProperty`
- `Domain` = `mmho:MiningAndMetallurgySite`
- `Range` = `xsd:string`（枚举值：全国重点 / 省级 / 市级 / 县级）
- 设计依据：中国文物保护四级制度

### 3. 设计决策

- 两条新属性均不挂父属性（CIDOC CRM 无对应，与 `mmho:conductedAtSite`/`mmho:excavatedFrom` 风格一致）
- Domain 统一为 `mmho:MiningAndMetallurgySite`（遗址级描述，由铜绿山 `mining_site.csv` 实例字段驱动）

### 4. 本体元素计数变化

| 类型               | v1.4.6 | v1.4.7 | 变化 |
| ---------------- | ------ | ------ | -- |
| DatatypeProperty | 0      | 2      | +2 |
| ObjectProperty   | 59     | 59     | 0  |
| Class            | 117    | 117    | 0  |
| NamedIndividual  | 232    | 232    | 0  |

---

## v1.4.8 详细改动

### `mmho:hasDimension` 的 Domain 扩展

原 `Domain` = `mmho:ExcavatedArtifacts`；扩展为 `unionOf(mmho:ExcavatedArtifacts, mmho:AnthropicFeature)`。

**设计依据**：矿冶实例化中"遗迹"（如高程、深度、间距）同样需要尺寸描述。铜绿山 `AnthropicFeature` 共 4 类尺寸 484 条记录，`hasDimension` 仅支持 `ExcavatedArtifacts` 会让这些记录在 schema 层失配。

Domain 扩展后，原已挂在 `ExcavatedArtifacts` 上的 `hasDimension` 关系与新挂在 `AnthropicFeature` 上的关系均合规，无需重新导入 Neo4j。

---

## v1.5.0 详细改动

### `hasVisualFeature` 与 `hasCondition` 的 Domain 扩展

- `mmho:hasVisualFeature`：Domain 从 `mmho:SmeltingProducts` 扩展到 `crm:E18_Physical_Thing`
- `mmho:hasCondition`：Domain 从 `mmho:SmeltingProducts` 扩展到 `crm:E18_Physical_Thing`

**设计依据**：孝民屯实例化中，铸范（`ExcavatedArtifacts`）上的纹饰 57 件、保存状况（烟炱/铜渣）需要 schema 层支持。两条属性的目标域统一为"物理事物"，覆盖所有物理子类（含 `ExcavatedArtifacts` 与 `SmeltingProducts`）。

与 `CIDOC CRM` 一致：`crm:P65_shows_visual_item` 与 `crm:P44_has_condition` 的 Domain 同为 `crm:E18_Physical_Thing`。

---

## v1.6.0 详细改动

### 1. `mmho:Type` 子类层级集中化设计

`mmho:ConservationLevel` 在 `v1.4.7` 引入时位于 `mmho:Type` 子类；`v1.6.0` 沿用此设计的扩展应用：所有"类型概念"统一通过 `mmho:Type` 间接继承 `crm:E55_Type`，不直接挂 `crm:E55_Type`。

现有 `mmho:Type` 子类：`ActivityType`/`ConditionType`/`DimensionType`/`ExcavatedArtifactsType`/`FunctionalSpatialRelationType`/`PlaceOfOriginType`/`VisualFeatureType` 等均遵循此模式。

`SPARQL` 查询可用 `?s rdfs:subClassOf* mmho:Type` 一句话匹配所有 mmho 类型概念。

### 2. 同步 `OWL/XML` 与 `Turtle` 两种序列化

本次发布统一本体在 `OWL/XML`（Protégé/Reasoner 加载）与 `Turtle`（人工可读/diff 友好）两种序列化下的当前状态。三元组计数与 rdf:type 一致，可通过 rdflib 6.x 互换。

### 3. 本体元素计数（rdflib 6.x 实测）

| 类型              | 值                           |
| --------------- | --------------------------- |
| 三元组             | 2,019                       |
| Class           | 121（98 mmho + 23 CIDOC CRM） |
| ObjectProperty  | 62（41 mmho + 21 CIDOC CRM）  |
| NamedIndividual | 241                         |

---

## 本体设计守则

1. **不预设具体枚举值**：本体只提供类型容器（Class / meta-type），具体类型由数据建模时根据考古报告语料添加为实例（`rdf:type`）
2. **类型概念走 `mmho:Type`**：所有"类型概念"通过 `mmho:Type` 间接继承 `crm:E55_Type`，不直接挂 `crm:E55_Type`
3. **punning Domain 按 CRMarchaeo 标准**：修饰关系本身的属性，Domain 引用 Property IRI（OWL 2 DL 允许）
4. **本体注释不含个人 / 时间戳 / 修订历史**：`rdfs:comment` 只描述设计依据，不含反馈时间、人物名、版本修订叙事

---

*格式约定：所有"本体元素计数"表格中的数字均为该版本 OWL/XML 文件在 rdflib 6.x 下的实测统计（v1.4.x 部分参考发布时 OntoMetrics 报告，新版本以当前实跑为准）。历史本体的完整快照可在 `ontology_history/` 子目录中找到对应版本比对。*
