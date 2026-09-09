# MMHO 本体变更日志

> **Mining and Metallurgy Heritage Ontology**
> 本体 IRI：`http://w3id.org/mmho`
> 命名空间前缀：`mmho:`
> 配套基础：`CIDOC CRM v7.1.3` + `CRMarchaeo v2.1`

---

## 版本时间线

| 版本 | 日期 | 核心改动 |
|---|---|---|
| 1.1.0 | 2026-06-12 | 原始版（`MMO_CRM_Merge_Final_Revised.owl`，已从桌面移除，未归档） |
| 1.2.0 | 2026-06-14 | 双语补全：所有 Class / ObjectProperty / NamedIndividual 加 zh + en label |
| 1.2.1 | 2026-06-14 | 加 CIDOC CRM 实体 IRI（`crm:` 前缀标准化） |
| 1.3.0 | 2026-07-14 | 新增 3 类关系：`hasPredecessor` / `hasSuccessor`（互逆对）、`hasFunctionalSpatialRelation`、`hasProcessEvolution` |
| 1.4.0 | 2026-07-14 | 新增 `hasFunctionalSpatialRelationType` 属性（FSRT 作为 Class + `subClassOf crm:E55_Type`，含 4 个硬编码枚举值） |
| 1.4.1 | 2026-07-16 | 中间过渡版（未独立归档，内容并入 v1.4.2 / v1.4.3 设计依据） |
| 1.4.2 | 2026-07-16 | 中间过渡版（未独立归档，直接演化为 v1.4.3） |
| **1.4.3** | 2026-07-17 | 修正 FSRT 设计错误（FSRT 改为 mmho:Type 子类，间接继承 crm:E55_Type） |
| **1.4.4** | **2026-07-17** | **补 7 个缺 label 的 Class + 改错别字 + 晋代/金代国际命名区分 + 修正 wasProducedAt Range 不匹配问题** |
| **1.4.5** | **2026-07-18** | **特性全面核对 + 解决 hasPredecessor/hasSuccessor 与 P69 的形式冲突 + belongsToPeriod 解除 Functional + identifiedByCoordinates/wasFoundAt 补 Functional** |

---

## v1.4.5 详细改动

### 1. 解决 hasPredecessor / hasSuccessor 与 hasAssociationWith 的 P69 形式冲突

蒋蒙 7-17 19:12 指出：
> "hasAssociationWith 是 P69 子属性、设为 symmetric，但 v1.3.0 加的 hasPredecessor/hasSuccessor 也是 P69 子属性，在 OWL 里会自动继承 symmetric 特性，破坏工序的有向性。"

**OWL 2 DL 严格语义分析：**
- `rdfs:subPropertyOf` 不传递 characteristic —— 所以 hasPredecessor 不会被自动推理成 symmetric
- 但 `hasAssociationWith`（Symmetric, sub=P69）会间接强化 P69 的对称性，与 hasPredecessor/hasSuccessor 的有向语义在形式上产生混淆（论文 4.3 节论述隐患）

**v1.4.5 改动：**
- `mmho:hasPredecessor` / `mmho:hasSuccessor`：**删除** `<rdfs:subPropertyOf crm:P69_has_association_with/>`，改为**独立属性**
- `mmho:hasSuccessor`：加 `<rdf:type owl:AsymmetricProperty/>`，明确有向性
  - OWL 2 DL 规则：`InverseOf(P, Q) ∧ P is Asymmetric ⇔ Q is Asymmetric`
  - 所以 `hasPredecessor` 通过 InverseOf 自动推理为 Asymmetric（无需显式声明）

**冲突解决后的 P69 子属性链：**

| 版本 | P69 子属性 | 备注 |
|---|---|---|
| v1.4.4 | 3 个（hasAssociationWith + hasPredecessor + hasSuccessor） | hasPredecessor/hasSuccessor 与 hasAssociationWith 形式"平级"，但语义完全不同（无向 vs 有向） |
| **v1.4.5** | **1 个（仅 hasAssociationWith）** | hasPredecessor/hasSuccessor 已独立，形式上与 P69 断绝关系 |

论文 4.3 节需明确说明三者的语义差异：hasAssociationWith（无向兜底）/ hasPredecessor·hasSuccessor（有向工序链）/ hasProcessEvolution（独立，工艺演化）。

### 2. `mmho:belongsToPeriod` 移除 Functional 声明

蒋蒙指出：矿冶遗址常**跨朝代**（如商代开始到汉代结束连续作业），`Functional` 强制"每个对象最多一个时期"会丢信息。

| 版本 | Functional | 跨朝代支持 | 语义 |
|---|---|---|---|
| v1.4.4 | ✓ | ❌ | 强制单时期（与考古实际冲突） |
| **v1.4.5** | **✗** | **✓** | 允许多时期实例，反映连续作业特征 |

Domain 三个类（`mmho:AnthropicFeature` / `mmho:ExcavatedArtifacts` / `mmho:MiningAndMetallurgySite`）保持不变。修改后每对象可挂多个 `mmho:Period` 实例，真实反映矿冶遗址的连续作业特征。

### 3. 补 `mmho:identifiedByCoordinates` + `mmho:wasFoundAt` Functional

蒋蒙建议补全两个"单值属性"的 Functional 声明：

| 属性 | 父属性 | Domain | Range | 新增 |
|---|---|---|---|---|
| `mmho:identifiedByCoordinates` | `crm:P168_place_is_defined_by` | `mmho:Location` | `mmho:GeographicCoordinates` | `owl:FunctionalProperty` |
| `mmho:wasFoundAt` | `crm:P53_has_former_or_current_location` | `mmho:ExcavatedArtifacts` | `mmho:Location` | `owl:FunctionalProperty` |

依据：每 Location 一组坐标、每件器物一个出土地 —— 这两个语义是"单值"的，Functional 符合数据建模守则。

### 4. v1.4.5 mmho ObjectProperty characteristic 分布

总计 37 个 mmho ObjectProperty（v1.4.4 → v1.4.5 不变）：

| characteristic | 个数 | 属性 |
|---|---|---|
| Symmetric | 1 | `hasAssociationWith` |
| Asymmetric | 1 | `hasSuccessor`（`hasPredecessor` 通过 InverseOf 自动推理） |
| Functional | 5 | `excavatedFrom`、 `hasExecutionTime`、 `hasReferenceNumber`、 `identifiedByCoordinates`（v1.4.5 新增）、 `wasFoundAt`（v1.4.5 新增） |
| Transitive | 2 | `hasBroaderTerm`、 `isPartOf` |
| Irreflexive | 1 | `excavatedFrom`（同时 Functional） |
| InverseOf 配对 | 1 对 | `hasPredecessor` ↔ `hasSuccessor` |
| 无 characteristic | 28 | 其余 |

**v1.4.4 → v1.4.5 characteristic 净变化：**
- 移除：1 个 Functional（`belongsToPeriod`）
- 新增：2 个 Functional（`identifiedByCoordinates`、 `wasFoundAt`）+ 1 个 Asymmetric（`hasSuccessor`）
- 净变化：Functional 4 → 5（+1）；Asymmetric 0 → 1（+1）

### 5. 不挂父属性的 mmho ObjectProperty 终极名单（v1.4.5 后：8 个）

| ObjectProperty | 不挂父属性的理由 |
|---|---|
| `mmho:conductedAtSite` | mmho 特有语义（"活动在某遗址进行"），CIDOC CRM 无对应 P |
| `mmho:excavatedFrom` | mmho 特有语义（"遗物来自哪个遗迹单元"），CIDOC CRM 无对应 P |
| `mmho:hasFunctionalSpatialRelation` | mmho 特有语义（"功能驱动的空间关系"），CRMarchaeo v2.1 AP11 是物理关系不适用 |
| `mmho:hasFunctionalSpatialRelationType` | CRMarchaeo v2.1 AP11.1 has type 的对应物（punning Domain） |
| `mmho:hasPredecessor` | **v1.4.5 新增**：避免与 hasAssociationWith (Symmetric) 形式平级，保留 InverseOf 即可 |
| `mmho:hasProcessEvolution` | 避免挂 crm:P69（对称性破坏有向性）；与 hasPredecessor/hasSuccessor 风格一致 |
| `mmho:hasSuccessor` | **v1.4.5 新增**：同 hasPredecessor |
| `mmho:wasProducedAt` | Range 类型不匹配：P53 Range=E53 Place ≠ mmho Range=E13 Attribute Assignment |

论文 4.x 节"本体父属性挂载策略"段需更新：从 6 个扩到 8 个，说明 v1.4.5 新增的两个独立属性。

### 6. 版本号

- `owl:versionInfo`：1.4.4 → **1.4.5**
- `dc:date`：2026-07-17 → **2026-07-18**

### 7. 本体元素计数变化

| 类型 | v1.4.4 | v1.4.5 | 变化 |
|---|---|---|---|
| Class | 134 | **134** | 0（不变） |
| NamedIndividual | 223 | **223** | 0（不变） |
| ObjectProperty | 57 | **57** | 0（不变，删除 2 行 + 新增 2 行） |
| 文件大小 | 189,369 B | **189,327 B** | -42 B（精确字符串替换，净删除 2 行 + 新增 2 行，各短 21 字符） |

---

## v1.4.3 详细改动

### 1. `mmho:FunctionalSpatialRelationType`（FSRT）类型修正

- **类型变化**：`<owl:NamedIndividual>` → `<owl:Class>`
- **父类变化**：`<rdfs:subClassOf crm:E55_Type>` → `<rdfs:subClassOf mmho:Type>`（间接继承 `crm:E55_Type`，通过 `mmho:Type → crm:E55_Type` 这条链）
- **删除**：v1.4.0 硬编码的 4 个具体枚举值（`mmho:Gravity-Fed-Spatial` 等）
- **承载方式**：具体功能空间关系类型由作者以后从考古报告语料中归纳后，作为 FSRT 的 `rdf:type` 实例（NamedIndividual）添加
- **配套属性**：`mmho:hasFunctionalSpatialRelationType` 的 Range 保持 `mmho:FunctionalSpatialRelationType`

### 2. 本体注释清理

- 移除所有 changelog 历史描述（v1.4.x 修订过程叙述）
- `rdfs:comment` 只保留设计依据（CRMarchaeo v2.1 AP11.1 标准做法）
- 数据建模规则描述不再包含具体类型实例

### 3. 数据建模示例改为数据建模规则

- 删除原 v1.4.2 数据建模示例中的具体类型举例（`ex:GravityFedRelationType` 等）
- 改为 3 条数据建模规则：
  1. 关系：`mmho:hasFunctionalSpatialRelation` 标注两个 `mmho:AnthropicFeature` 实例之间的功能空间关系
  2. 类型标注：`mmho:hasFunctionalSpatialRelationType` 的 value 是 `mmho:FunctionalSpatialRelationType` 的实例
  3. 具体类型由作者从考古报告语料中归纳后添加，本体不预设具体类型

### 4. 版本号

- `owl:versionInfo`：1.4.2 → **1.4.3**
- `dc:date`：2026-07-16 → **2026-07-17**

### 5. 本体元素计数变化

| 类型 | v1.4.2 | v1.4.3 | 变化 |
|---|---|---|---|
| Class | 133 | **134** | +1（FSRT 改回 Class） |
| NamedIndividual | 224 | **223** | -1（FSRT 不再是 NI） |
| ObjectProperty | 57 | **57** | 0 |
| 文件大小 | 187,930 B | **186,985 B** | -945 B（changelog 文本删除） |

---

## v1.4.4 详细改动

### 1. 补 7 个缺 label 的 Class

7 个 v1.2.0 时代遗留的"基本元素类"缺中英文 label：

| Class | 父类 | zh label | en label |
|---|---|---|---|
| `mmho:Bronze` | CopperAlloys | 青铜 | Bronze |
| `mmho:Copper` | CopperAndCopperAlloys | 纯铜 | Copper |
| `mmho:Iron` | IronAndFerroalloys | 铁 | Iron |
| `mmho:Steel` | IronAndFerroalloys | 钢 | Steel |
| `mmho:Lead` | MetallicMaterials | 铅 | Lead |
| `mmho:Tin` | TinAndTinAlloys | 锡 | Tin |
| `mmho:Cupellation` | SmeltingTechniques | 灰吹法 | Cupellation |

### 2. 错别字修正

- `mmho:GeographicCoordinates` 的英文 label 从 `Geographic Cordinates` 改为 `Geographic Coordinates`

### 3. 晋代 / 金代国际命名区分

为避免与国际上同名朝代冲突，按西方学术标准命名：

| 改前 | 改后 | 英文 label |
|---|---|---|
| `mmho:JinDynasty`（晋代 266-420） | `mmho:JinDynasty_WesternJin` | Western Jin Dynasty |
| `mmho:JinDynasty2`（金代 1115-1234） | `mmho:JinDynasty_JurchenJin` | Jurchen Jin Dynasty |

依据：Wikipedia / Britannica 标准用法——西方学术界用 `Western Jin` 和 `Jurchen Jin` 区分两个 Jin dynasty。

### 4. 修正 `mmho:wasProducedAt` Range 类型不匹配问题

| 版本 | 父属性 | Range | 是否匹配 |
|---|---|---|---|
| v1.3.0 - v1.4.3 | crm:P53_has_former_or_current_location | mmho:InferredPlaceOfOrigin (E13) | ❌ P53 Range=E53 Place ≠ E13 |
| **v1.4.4** | **（无父属性）** | mmho:InferredPlaceOfOrigin (E13) | ✓ 独立属性，CIDOC CRM 无合适 P |

设计依据：crm:P53 的 Range 是 crm:E53 Place（地理位置），但 `wasProducedAt` 的 Range 是 mmho:InferredPlaceOfOrigin（crm:E13 Attribute Assignment 子类，建模"推断"这一行为本身）—— Range 类型不匹配。改为独立属性，跟 mmho:hasFunctionalSpatialRelation / mmmo:hasProcessEvolution 风格一致。

### 5. 设计决策：保持 mmho:Period 父类 E41_Appellation 不变

经过详细讨论后决定**保持现状**。理由：

- **设计哲学清晰**：粗粒度时段（朝代名）= crm:E41 Appellation（标识/称谓）；细粒度时间 = mmho:SpecificYear (E62 String) / mmho:ExcavationInvestigationTime (E52 Time-Span)
- **无冲突**：当前 mmho:belongsToPeriod (P1) + mmho:hasPeriodRelatedNotes (P3) + mmho:hasSpecificYear (P3) + mmho:hasExecutionTime (P4) 四件套配合完美
- **改过去反而混淆**：crm:E4 Period + crm:P8i was taken place on or within 虽然类型匹配，但会把粗细粒度混在 Period 一个类里，反而没有现状清晰
- **CRMarchaeo 自身也是两套体系**：E4 Period + E52 Time-Span + E5 Event 都有

论文 4.x 节"本体父属性挂载策略"段应明确说明此设计决策的依据。

### 6. 经核对确认不挂父属性的 mmho ObjectProperty（最终：6 个）

| ObjectProperty | 不挂父属性的理由 |
|---|---|
| `mmho:conductedAtSite` | mmho 特有语义（"活动在某遗址进行"），CIDOC CRM 无对应 P |
| `mmho:excavatedFrom` | mmho 特有语义（"遗物来自哪个遗迹单元"），CIDOC CRM 无对应 P |
| `mmho:hasFunctionalSpatialRelation` | mmho 特有语义（"功能驱动的空间关系"），CRMarchaeo v2.1 AP11 是物理关系不适用 |
| `mmho:hasProcessEvolution` | 挂 crm:P69 会破坏有向性（P69 是 symmetric） |
| `mmho:hasFunctionalSpatialRelationType` | CRMarchaeo v2.1 AP11.1 has type 的对应物（punning Domain） |
| **`mmho:wasProducedAt`（v1.4.4 新增）** | **Range 类型不匹配：P53 Range=E53 Place ≠ mmho Range=E13 Attribute Assignment** |

论文 4.x 节"本体父属性挂载策略"段需明确说明每个不挂父属性的设计依据，避免读者怀疑是疏漏。

### 7. 版本号

- `owl:versionInfo`：1.4.3 → **1.4.4**
- `dc:date`：2026-07-17 → **2026-07-17**（同日升级）

### 8. 本体元素计数变化

| 类型 | v1.4.3 | v1.4.4 | 变化 |
|---|---|---|---|
| Class | 134 | **134** | 0（ID 重命名不影响计数；加 label 不影响） |
| NamedIndividual | 223 | **223** | 0 |
| ObjectProperty | 57 | **57** | 0 |
| 文件大小 | 186,985 B | **189,369 B** | +2,384 B（label / comment / ID 重命名） |

---

## 设计依据：CRMarchaeo v2.1 AP11.1 has type 标准做法

`mmho:hasFunctionalSpatialRelationType` 属性的设计严格对应 CRMarchaeo v2.1（CIDOC CRM 考古扩展）的 AP11.1 has type 标准模式：

| CRMarchaeo v2.1 | MMHO v1.4.3 | 说明 |
|---|---|---|
| `AP11.1 has type` | `mmho:hasFunctionalSpatialRelationType` | 类型标注属性 |
| Domain = `AP11 has physical relation to`（Property IRI） | Domain = `mmho:hasFunctionalSpatialRelation`（Property IRI） | **punning Domain**：Domain 引用的是属性 IRI 而非 Class IRI。OWL 2 DL 允许——OWL 引擎会自动把被引用的 IRI 同时当作 Class 和 Property 用 |
| Range = `crm:E55_Type` | Range = `mmho:FunctionalSpatialRelationType` | FSRT 是 `mmho:Type` 的子类，通过 `mmho:Type → crm:E55_Type` 间接继承 |

**为什么 FSRT 不直接挂 `crm:E55_Type` 而走 `mmho:Type`：**
- `mmho:Type` 是 mmho 本体的"类型概念基础类"，集中所有 mmho 自己的分类概念
- 6 个现有 Type 子类（`mmho:ActivityType` / `mmho:ConditionType` / `mmho:DimensionType` / `mmho:ExcavatedArtifactsType` / `mmho:PlaceOfOriginType` / `mmho:VisualFeatureType`）都只挂 `mmho:Type`
- FSRT 作为第 7 个 Type 子类，架构一致；SPARQL 查询可用 `?s rdfs:subClassOf* mmho:Type` 一句话匹配所有 mmho 类型
- 避免 7 个 Type 子类都直接挂 `crm:E55_Type` 的冗余继承

**punning Domain 的工作原理（OWL 2 DL 标准）：**
- 当一个属性 P1 的 Domain 引用了另一个属性 P2 的 IRI（不是 Class 的 IRI）时，OWL 2 DL 允许这种用法
- 隐含语义：P2 这个 IRI **同时**被当作 Class（用于 Domain 的"类型"约束）和 Property（用于实际的关系连接）
- Protégé 在 Class Hierarchy 视图里会临时显示该 IRI（看起来"很怪"），但这是 CRMarchaeo v2.1 的标准做法

**CRMarchaeo v2.1 实例**（蒋蒙论文第二章引用）：
```
The wall C (A8) has physical relation to wall D (A8) has type abuts on (E55).
```
- `AP11 has physical relation to`（Property IRI）：连接两个 A8 Stratigraphic Unit 实例
- `AP11.1 has type` 的 Domain = `AP11 has physical relation to`（punning Domain）
- `abuts on` 是 `crm:E55_Type` 的实例（具体类型）

---


## v1.4.6 详细改动

### 1. 朝代类 → 朝代实例（与论文 3.2.3 节"时代概念作为实例"对齐）

**问题**：v1.4.5 沿用 v1.0 时期 18 个朝代 Class 的设计，但论文 段 121 明确说"时代概念（如商代、西周、汉代）作为时序模块的实例"。v1.4.5 实际有 18 个顶级朝代 Class + 22 个朝代细分 NamedIndividual（指向旧 Class）+ 4 个孤儿 NI（Han_Dynasty / Qin_Dynasty / Shang_Dynasty / Xia_Dynasty 无 rdf.type），与论文 段 121 矛盾。

**v1.4.5 旧 ID 残留问题（蒋蒙 7-22 13:50 指出）**：7-17 升级 v1.4.4 时把 `mmho:JinDynasty` 拆为 `_WesternJin` / `_JurchenJin`，但**旧 `mmho:JinDynasty` Class 没删**，下面的实例（Eastern_Jin / Western_Jin）也没跟着迁移到新 Class——这是 v1.4.5 升级时检查漏项（7-17 daily 写"旧 ID 残留引用 0 处"时只查了 NI/OP Domain/Range，没查 rdf.type）。

**v1.4.6 改动**：
- **删除 18 个朝代 Class**：`HanDynasty` / `JinDynasty` / `JinDynasty_WesternJin` / `JinDynasty_JurchenJin` / `LiaoDynasty` / `MingDynasty` / `QinDynasty` / `QingDynasty` / `ShangDynasty` / `SongDynasty` / `SouthernNorthernDynasties` / `SuiDynasty` / `TangDynasty` / `ThreeKingdoms` / `FiveDynastiesTenKingdoms` / `WesternXia` / `XiaDynasty` / `YuanDynasty` / `ZhouDynasty`
- **统一指向 `mmho:Period`**：34 处原 `rdf.type` 从旧朝代 Class 改为 `mmho:Period`（mmho:Period → crm:E41_Appellation，作为朝代实例的归属类）
- **4 个孤儿 NI 补 rdf.type**：`Han_Dynasty` / `Qin_Dynasty` / `Shang_Dynasty` / `Xia_Dynasty` → 全部 `rdf.type mmho:Period`
- **新增 19 个主朝代 NamedIndividual**（zh + en label + zh comment）：夏 / 商 / 周 / 秦 / 汉 / 三国 / 西晋 / 东晋 / 南北朝 / 隋 / 唐 / 五代十国 / 宋 / 辽 / 金 / 西夏 / 元 / 明 / 清
- **新增 13 个朝代细分 NamedIndividual**：商代早期 / 商代中期 / 商代晚期 / 西周 / 东周 / 西汉 / 东汉 / 北宋 / 南宋 / 唐初 / 盛唐 / 中唐 / 晚唐

**mmho:Period 实例总数**：v1.4.5 0 个 → **v1.4.6 63 个**（含原 18 + 新增 32 + 春秋战国细分等已有 NI 自动归位）

### 2. 器物组合件（NI 版实现）

**问题**：v1.4.5 完全删除器物组合件（v1.0 时期 附录H 的 `mmho:ArtifactAssemblage` Class + `mmho:consistsOfComponent` ObjectProperty 全部丢失），但论文 段 251 明确描述了器物组合件概念。

**v1.4.6 改动（NI 版，不加 Class）**：
- **新增 ObjectProperty**：`mmho:consistsOfComponent`（自指，Domain=Range=mmho:FinalProducts, subPropertyOf crm:P46i_forms_part_of）
- **新增 3 个示范组合件 NI**：
  - `BianZhongSet_001`（某编钟组合）→ consistsOfComponent: 甬钟、钮钟
  - `CheMaQiSet_001`（某车马器套件）→ consistsOfComponent: 轴套（AxleSleeve）、马衔（HorseBit）、轭（Yoke）
  - `JiuQiSet_001`（某酒器组合）→ consistsOfComponent: 爵（Jue）、觚（Gu）、斝（Jia）
- **新增 2 个 FinalProducts NI**（段 251 提到但之前缺失）：`YongZhong`（甬钟）/ `NiuZhong`（钮钟）

### 3. v1.4.6 OntoMetrics 评估

| 指标 | v1.4.5 | v1.4.6 | 变化 |
|---|---|---|---|
| Class（含 CRM） | 134 | 116 | -18（删 18 个朝代 Class） |
| Class（mmho） | 112 | 94 | -18 |
| ObjectProperty（含 CRM） | 57 | 58 | +1（consistsOfComponent） |
| ObjectProperty（mmho） | 37 | 38 | +1 |
| NamedIndividual | 223 | 247 | +24 |
| mmho:Period 实例 | 0 | 63 | +63 |
| 总公理 | 1844 | 1895 | +51 |
| 逻辑公理 | 460 | 475 | +15 |
| SubClassOf 公理 | 115 | 97 | -18（删朝代 Class） |
| SubPropertyOf 公理 | 29 | 30 | +1 |
| Domain 公理 | 45 | 46 | +1 |
| Range 公理 | 37 | 38 | +1 |
| DL Expressivity | SOIFI | SOIFI | 不变 |
| Functional | 5 | 5 | 不变 |
| Transitive | 2 | 2 | 不变 |
| Asymmetric | 1 | 1 | 不变 |
| Symmetric | 1 | 1 | 不变 |
| Irreflexive | 1 | 1 | 不变 |
| 绝对根节点 | 23 | 23 | 不变 |
| 最大深度 | 5 | 5 | 不变 |
| 平均深度 | 2.44 | 2.52 | +0.08（删叶子后平均值上移） |
| 绝对叶节点 | 91 | 74 | -17 |
| 缠绕度 | 0.000 | 0.000 | 不变 |

### 4. 论文需改清单（升级到 v1.4.6）

- **段 191 / 段 473 / 段 487 / 段 493 / 段 494 / 段 495**：v1.4.5 → **v1.4.6**（版本号）
- **段 493 "类映射采用rdfs:subClassOf建立层级关系，134个类...111个mmho类采用直接映射，1个mmho类采用特殊映射"**：
  - 134 → **116** 类
  - 112 mmho 类 → **94** mmho 类
  - 111 直接映射 + 1 特殊映射 = 112 → 需要重新数实际直接映射数（删 18 个朝代 Class 后，mmho:Period 不再作为父类被 18 个朝代 Class 引用）
- **段 493 "8个属性作为领域特定扩展定义"**：v1.4.5 8 个 + 新增 1 个（consistsOfComponent）= **9 个**
- **段 473 "实例数量223个"**：223 → **247** 个
- **段 251 器物组合件**：保留原描述，加一句"在 v1.4.6 本体中以 NamedIndividual + mmho:consistsOfComponent 自指属性建模（无独立类）"
- **段 138-140 三大角色 8 核心类 + 60 子类 + 72 实例 = 140**：
  - 实际 v1.4.6 mmho Class 94 个（删 18 朝代 Class 后），仍需核对"8 核心类 / 60 子类"是否仍对应——可能 60 子类 也不准了
- **段 4.3.1 时空核心类体系**："时代"段需重写——v1.4.5 时代是 18 个 Class 顶层挂着，v1.4.6 改为 mmho:Period 单类，下挂 63 个实例 NI

### 5. 设计决策（重要，不要忘）

- **朝代走 NI（不挂 Class）**：跟论文 段 121 "时代概念作为实例" 对齐
- **mmho:Period 父类保持 crm:E41_Appellation**（v1.4.4 决策不变）：粗粒度时段（朝代名）= E41 Appellation（标识），细粒度时间 = SpecificYear (E62 String) / ExcavationInvestigationTime (E52 Time-Span)
- **器物组合件走 NI + 自指属性（不加 Class）**：避免强行分类，跟论文 段 154 "本本体中将不进行进一步的功能分类" 一致
- **mmho:consistsOfComponent Domain=Range=mmho:FinalProducts 自指**：组合件本身就是 FinalProducts NI，组件也是 FinalProducts NI；subPropertyOf crm:P46i_forms_part_of 兼容 CIDOC CRM 语义

### 6. 教训（v1.4.6 新增）

- **重命名要彻底删旧**：7-17 把 `mmho:JinDynasty` 拆为 `_WesternJin` / `_JurchenJin` 时，**旧 `mmho:JinDynasty` Class 没删**，下面的 `Eastern_Jin` / `Western_Jin` 实例还指向旧 Class——7-17 升级时只查了"NI/OP Domain/Range 引用"，**漏查 rdf.type 引用**。以后重命名 Class，必须**先扫描 rdf.type 引用并迁移**再删旧 Class
- **Class 顶层挂"空 NI" 是设计缺陷**：v1.4.5 有 4 个孤儿 NI（Han_Dynasty / Qin_Dynasty / Shang_Dynasty / Xia_Dynasty）完全没有 rdf.type——表明升级时漏检。以后 Class 升级后必须跑 OntoMetrics 报"Class assertion axioms 异常低"作为预警
- **对象属性不需要显式 rdf.type**：v1.4.6 第一版我给 `mmho:consistsOfComponent` 加了 `<rdf:type rdf:resource="...owl#ObjectProperty"/>`，owlready2 报警告——`<owl:ObjectProperty rdf:about="...">` 已经隐含类型，多余 rdf.type 是错的
- **NI 组件名要先验证存在再连**：v1.4.6 第一版我给 BianZhongSet_001 配了 `YongZhong_001` / `NiuZhong_001` 等不存在的 NI，组件全是空——以后新加 NI + 关联 OP 必须在同一脚本中验证

---



## v1.4.6 详细改动 (2026-07-22)

### 1. 朝代类 → 朝代实例 (与论文 段 121"时代概念作为实例"对齐)

**问题**: v1.4.5 沿用 v1.0 时期 18 个朝代 Class 的设计, 但论文 段 121 明确说"时代概念(如商代、西周、汉代)作为时序模块的实例"。v1.4.5 实际有 18 个顶级朝代 Class + 22 个朝代细分 NI + 4 个孤儿 NI, 跟论文 段 121 矛盾。

**v1.4.5 旧 ID 残留 (蒋蒙 7-22 13:50 指出)**: 7-17 升级 v1.4.4 时把 `mmho:JinDynasty` 拆为 `_WesternJin` / `_JurchenJin`, 但**旧 `mmho:JinDynasty` Class 没删**, 下面的实例 (Eastern_Jin / Western_Jin) 也没迁移——7-17 daily 写"旧 ID 残留引用 0 处"时只查了 NI/OP Domain/Range, 没查 rdf.type。

**v1.4.6 改动**:
- **删 18 个朝代 Class**: HanDynasty / JinDynasty / JinDynasty_WesternJin / JinDynasty_JurchenJin / LiaoDynasty / MingDynasty / QinDynasty / QingDynasty / ShangDynasty / SongDynasty / SouthernNorthernDynasties / SuiDynasty / TangDynasty / ThreeKingdoms / FiveDynastiesTenKingdoms / WesternXia / XiaDynasty / YuanDynasty / ZhouDynasty
- **34 处 rdf.type 从旧朝代 Class 改为 mmho:Period**
- **4 个孤儿 NI 补 rdf.type**: Han_Dynasty / Qin_Dynasty / Shang_Dynasty / Xia_Dynasty
- **新增 19 个主朝代 NI**: 夏 / 商 / 周 / 秦 / 汉 / 三国 / 西晋 / 东晋 / 南北朝 / 隋 / 唐 / 五代十国 / 宋 / 辽 / 金 / 西夏 / 元 / 明 / 清
- **新增 13 个朝代细分 NI**: 商早期/中期/晚期、西周/东周、西汉/东汉、北宋/南宋、初唐/盛唐/中唐/晚唐
- **删 18 个 legacy 重复名**: Han_Dynasty, Shang_Dynasty, Xia_Dynasty, Qin_Dynasty, Northern_Song, Southern_Song, Early_Tang, High_Tang, Middle_Tang, Late_Tang, Western_Jin, Eastern_Jin, Early/Late/Middle_Spring_and_Autumn_Period, Early/Late_Warring_States_Period

**mmho:Period 实例总数**: v1.4.5 0 个 → v1.4.6 47 个 (顶级 18 + 子级 29)

### 2. 朝代命名 (按论文 附录F 时代 Period 树)

**附录F 顶层**: 夏 / 商 / 周 / 秦 / 汉 / 三国 / 晋代 / 南北朝 / 隋 / 唐 / 五代十国 / 宋 / 辽 / 西夏 / 金 / 元 / 明 / 清

**附录F 子层**:
- 晋代 ← 西晋 / 东晋 / 十六国
- 南北朝 ← 南朝 / 北朝
- 唐 ← 初唐 / 盛唐 / 中唐 / 晚唐
- 宋 ← 北宋 / 南宋

**命名决策**:
- `mmho:JinDynasty` (zh: 晋代, en: "Jin Dynasty") — 顶级, 涵盖 266-420 整个晋朝
- `mmho:WesternJin` (zh: 西晋, en: "Western Jin") — `JinDynasty` 子级, 266-316
- `mmho:EasternJin` (zh: 东晋, en: "Eastern Jin") — `JinDynasty` 子级, 317-420
- `mmho:SixteenKingdoms` (zh: 十六国, en: "Sixteen Kingdoms") — `JinDynasty` 子级, 304-439 (与东晋重叠)
- `mmho:JurchenJin` (zh: 金代, en: "Jin Dynasty") — 顶级, 1115-1234 (避免跟 `JinDynasty` 命名冲突)

**WesternJin / EasternJin 拆分依据**: 7-22 14:44 蒋蒙提供的国际标准 (Wikipedia/Britannica/学术论文): 晋代 (266-420) = "Jin Dynasty", 可拆为 Western Jin (266-316) + Eastern Jin (317-420)。附录F 把它们放在"晋代"下, 跟论文 段 251 "器物组合件"风格一致 (TBox 类型概念, 留 ABox 填充具体数据)。

### 3. 朝代层级 (mmho:hasBroaderPeriod)

**新加 ObjectProperty**: `mmho:hasBroaderPeriod`
- Domain: mmho:Period
- Range: mmho:Period
- Transitive
- **不挂 crm:P127** (避免 mmho:Period ≠ crm:E55_Type 的类型冲突)
- Label zh: 拥有上位时期, en: has broader period

**29 条 hasBroaderPeriod 关系** (按附录F 时代树, 方向: 子→父):
```
商 ← 早商/中商/晚商
  早商 ← 二里岗下层/二里岗上层
  晚商 ← 殷墟一期/二期/三期/四期
周 ← 西周/东周
  东周 ← 春秋/战国
汉 ← 西汉/东汉
三国 ← 魏/蜀/吴
晋代 ← 西晋/东晋/十六国
南北朝 ← 南朝/北朝
唐 ← 初唐/盛唐/中唐/晚唐
宋 ← 北宋/南宋
```

**方向修正 (蒋蒙 7-22 14:51 指出)**: 早期版本方向反了 (`<ThreeKingdoms> hasBroaderPeriod <Wei>`), 全部 29 条反转, 现 `<魏> hasBroaderPeriod <三国>` (子→父, 正确)。

**与 mmho:hasBroaderTerm 区分**: 
- `mmho:hasBroaderTerm`: Domain=Range=mmho:Type (TBox 通用层级, subPropertyOf crm:P127)
- `mmho:hasBroaderPeriod`: Domain=Range=mmho:Period (朝代层级, **不挂 CRM**, 避免 E55_Type vs E41_Appellation 类型冲突)

### 4. 器物组合件 (TBox Class 版, 论文 段 251)

**问题**: v1.4.5 完全删除器物组合件 (v1.0 时期 附录H 的 `mmho:ArtifactAssemblage` Class + `mmho:consistsOfComponent` ObjectProperty 全部丢失), 但论文 段 251 明确描述了器物组合件概念。

**v1.4.6 改动 (走 TBox Class 版, 不走 ABox NI 版)**:
- **新增 Class**: `mmho:ArtifactAssemblage` (subClassOf `mmho:FinalProducts`, zh: 器物组合件, en: Artifact Assemblage)
- **新增 ObjectProperty**: `mmho:consistsOfComponent` (Domain=`mmho:ArtifactAssemblage`, Range=`mmho:FinalProducts`, subPropertyOf `crm:P46i_forms_part_of`)
- **新增 2 个器型类型 NI**: `mmho:YongZhong` (甬钟), `mmho:NiuZhong` (钮钟) — 跟 鼎/簋/爵 一样是器型

**ABox 边界**: 具体某一组 (如"曾侯乙编钟组合") 应在 ABox 数据填充时作为 `mmho:ArtifactAssemblage` 的实例建模, **不在 TBox 领域本体里**。

**经验 (蒋蒙 7-22 15:09 指出)**: 之前一度走了 NI 版 + 加 3 个具体实例 NI (BianZhongSet_001/CheMaQiSet_001/JiuQiSet_001) — **违反 TBox 原则**, 已删。**领域本体只定义类型概念, 不放具体数据点**。

### 5. 二里岗映射修正

**问题**: v1.4.5 之前 ErligangLower→MiddleShang, ErligangUpper→LateShang — 错位。

**正确** (按国际考古分期):
- `ErligangLower` (二里岗下层, ~BC 1600-1400) → `EarlyShang` (商代早期)
- `ErligangUpper` (二里岗上层, ~BC 1400-1300) → `MiddleShang` (商代中期)

### 6. 动物纹饰命名统一 (蒋蒙 7-22 15:25)

5 个 Animal Motif Pattern NI 中文 label 加"纹"字 (跟父类"现实动物纹饰"命名一致):
- 鸮 → 鸮纹
- 猪 → 猪纹
- 象 → 象纹
- 犀 → 犀纹
- 虎 → 虎纹

English label 加 "Motif" (跟父类 Animal Motif Pattern 对应): Owl Motif / Pig Motif / Elephant Motif / Rhinoceros Motif / Tiger Motif。

### 7. 其他清理

- **删 owl:unionOf 残留**: 7-22 14:27 改 hasBroaderTerm Domain/Range 为 union class 时, 匿名 Class 错位成 hasBroaderTerm 的直接子元素; rollback 没清干净, 14:51 删除 2 个匿名 Class 残留
- **删 2 个孤儿 Province NI**: `Jiangxi_Province`, `Shanxi_Province` (zh label 跟 `Jiangxi`/`Shanxi` 重复, 无 rdf.type)
- **删 `Xiao` 多余 zh label "鸮"**: 保留 v1.4.6 新加的"鸮纹", 删 v1.4.5 原版遗留的"鸮"
- **删 18 个 legacy 重复名** (重复 v1.4.5→v1.4.6 删除朝代 Class 后留下的孤儿 NI)

### 8. v1.4.6 OntoMetrics 评估

| 指标 | v1.4.5 | v1.4.6 | 变化 |
|---|---|---|---|
| Class (含 CRM) | 134 | 116 | -18 (删 18 朝代 Class + 1 增 ArtifactAssemblage) |
| Class (mmho) | 112 | 94 | -18 |
| ObjectProperty (含 CRM) | 57 | 58 | +1 (consistsOfComponent) |
| ObjectProperty (mmho) | 37 | 38 | +1 |
| NamedIndividual | 223 | 226 | +3 (+47 Period, +2 器型, -18 朝代 Class 删, -3 demo, -2 孤儿, -2 重复 zh, -1 子) |
| mmho:Period 实例 | 0 | 47 | +47 |
| mmho:AnimalMotifPattern NI | 5 | 5 | 0 (改名 5 个) |
| 总公理 | 1844 | 1822 | -22 |
| 逻辑公理 | 460 | 458 | -2 |
| SubClassOf | 115 | 98 | -17 (删 18 朝代 Class + 1 增 ArtifactAssemblage) |
| SubPropertyOf | 29 | 30 | +1 (consistsOfComponent → P46i) |
| Domain | 45 | 47 | +2 (consistsOfComponent, hasBroaderPeriod) |
| Range | 37 | 39 | +2 (consistsOfComponent, hasBroaderPeriod) |
| Functional | 5 | 5 | 0 |
| Transitive | 2 | 3 | +1 (hasBroaderPeriod) |
| Asymmetric | 1 | 1 | 0 |
| Symmetric | 1 | 1 | 0 |
| Irreflexive | 1 | 1 | 0 |
| DL Expressivity | SOIFI | SOIFI | 0 |
| 绝对根 | 23 | 23 | 0 |
| 最大深度 | 5 | 5 | 0 |
| 平均深度 | 2.44 | 2.52 | +0.08 (删 18 朝代 Class 叶子) |
| 绝对叶 | 91 | 74 | -17 (删朝代 Class 叶子) |
| 缠绕度 | 0.000 | 0.000 | 0 |
| 继承丰富度 | 1.027 | 1.032 | +0.005 |
| 关系丰富度 | 0.243 | 0.287 | +0.044 |

### 9. 论文需改清单 (升级到 v1.4.6)

#### 9.1 版本号 (多处)
- 段 235, 237, 239: v1.4.5 → v1.4.6
- 段 473, 487, 493, 494, 495: v1.4.5 → v1.4.6

#### 9.2 数值类
- **段 473**: 134 类 → **116 类**; 112 mmho → **94 mmho**; 223 实例 → **226 实例**; 1844 → 1822 公理; 460 → 458 逻辑公理
- **段 478**: SubObjectPropertyOf 29 → **30**; Functional 5 (不变); Transitive 2 → **3**; Asymmetric 1; Symmetric 1; Irreflexive 1
- **段 482**: 关系丰富度 0.243 → **0.287**; 继承丰富度 1.027 → **1.032**; 公理/类比 16.464 → **19.298**; 类/关系比 3.03 → **2.474**
- **段 486**: 平均深度 2.44 → **2.52**; 绝对叶 91 → **74**; 最大深度 5 (不变); 绝对根 23 (不变); 缠绕度 0 (不变)
- **段 487**: 134 → **116 类** (mmho 94); 57 → **58 属性** (mmho 38); 223 → **226 实例**; 函数式 5 (不变); 传递性 2 → **3**; 对称性 1; 非对称性 1; 反自反性 1
- **段 493**: 134 → **116 类**; 112 → **94 mmho 类**; 8 个扩展定义属性 → **9 个** (+1 consistsOfComponent); 29 + 8 = 37 mmho OP (不变)
- **段 494**: 134 → **116 类**; 57 → **58 属性**; 223 → **226 实例**; 1844 → **1822 公理**; 5 函数式 / 2 → **3 传递性** / 1 对称 / 1 非对称 / 1 反自反
- **段 495**: 134 → **116 类**; 57 → **58 属性**; 223 → **226 实例**

#### 9.3 朝代本体段 (段 121 + 段 251)
- 论文 段 138-140 "三大角色 (8 核心类 + 60 子类 + 72 实例 = 140)" 需重算: v1.4.6 实际 mmho Class 94 个, 跟 140 概念数对不上 (此问题跟 v1.4.6 升级无关, 是 7-22 13:38 已发现)
- 段 251 器物组合件: 加一句"在 v1.4.6 本体中以 mmho:ArtifactAssemblage Class (subClassOf mmho:FinalProducts) 建模, 配合 mmho:consistsOfComponent 属性 (Domain=ArtifactAssemblage, Range=FinalProducts, subPropertyOf crm:P46i); 具体某一组在 ABox 数据填充时建模"
- 段 154 "将'鼎''簋''爵'等器物做为'最终产品'下的实例" 保持不变

#### 9.4 不挂父属性的 mmho OP 名单 (4.x 节, 段 326 引用)
- v1.4.5: 8 个 (mmho:conductedAtSite, excavatedFrom, hasFunctionalSpatialRelation, hasFunctionalSpatialRelationType, hasPredecessor, hasProcessEvolution, hasSuccessor, wasProducedAt)
- v1.4.6: **9 个** (+mmho:hasBroaderPeriod) — 不挂父属性因为 mmho:Period 是 E41_Appellation (非 E55_Type), crm:P127 不适用

### 10. 设计决策 (重要, 不要忘)

- **朝代走 NI** (论文 段 121 一致)
- **mmho:Period 父类保持 crm:E41_Appellation** (v1.4.4 决策不变)
- **晋/金 命名**: `JinDynasty` vs `JurchenJin` — 避免 "Jin" 命名冲突, Western/Eastern 是晋朝内部分期 (西晋 266-316, 东晋 317-420)
- **器物组合件走 TBox Class** (不挂具体 ABox 实例)
- **mmho:hasBroaderPeriod 不挂 crm:P127** (类型冲突)
- **mmho:hasBroaderPeriod 方向: 子→父**

### 11. 教训 (v1.4.6 新增)

- **重命名要彻底删旧**: 7-17 把 mmho:JinDynasty 拆为 _WesternJin / _JurchenJin 时, 旧 mmho:JinDynasty Class 没删, 下面的 Eastern_Jin / Western_Jin 实例还指向旧 Class——7-17 升级时只查了 NI/OP Domain/Range 引用, **漏查 rdf.type 引用**。以后重命名 Class, 必须**先扫描 rdf.type 引用并迁移**再删旧 Class
- **Class 升级后必须扫描 rdf.type 引用**: v1.4.5 有 4 个孤儿 NI (Han_Dynasty / Qin_Dynasty / Shang_Dynasty / Xia_Dynasty) 完全没有 rdf.type——表明升级时漏检
- **对象属性不需要显式 rdf.type**: 给 mmho:ArtifactAssemblage Class 加 `<rdf:type rdf:resource="...owl#Class"/>` 导致 Protégé 解析挂掉——`<owl:Class>` 元素本身已隐含类型, 多余 rdf.type 是错的
- **NI 组件名要先验证存在再连**: BianZhongSet_001 第一版配了不存在的 YongZhong_001 / NiuZhong_001, 组件全是空
- **NI 版 demo 放具体实例违反 TBox 原则**: 7-22 15:09 蒋蒙指出 BianZhongSet_001 等是 ABox 数据, 不在 TBox 领域本体里
- **hasBroaderPeriod 方向**: 子→父 (X 拥有更上位的 Y, X 永远是子级, Y 永远是父级)
- **rollback 不彻底**: 改 hasBroaderTerm Domain/Range 为 union class 时, 匿名 Class 错位成 hasBroaderTerm 的直接子元素; rollback 删 rdfs:domain/range 时没清掉残留

## v1.4.7 详细改动 (2026-08-14)

### 1. 升级背景

**驱动**：8-13 daily 指出铜绿山 `mining_site.csv` 中两个字段没有对应 mmho 属性：
- `面积(平方公里)` = 7.8（铜绿山遗址）
- `保护级别` = "全国重点文物保护单位（1982 年第一批）"

8-13 时决定"回头让改时再改"，8-14 20:10 启动升级。

### 2. 新增 mmho:hasArea（遗址面积）

- **类型**：DatatypeProperty（mmho 本体第一个 DatatypeProperty，打破此前 0 DatatypeProperty 的传统）
- **Domain**：mmho:MiningAndMetallurgySite
- **Range**：xsd:decimal
- **设计依据**：铜绿山遗址 7.8 平方公里需要直接用数值表示，不能用 ObjectProperty 嵌套 E54_Dimension。
- **comment (en)**：Area of the mining and metallurgy site, expressed in square kilometers.
- **comment (zh)**：遗址面积，数值记录，单位平方公里。

### 3. 新增 mmho:hasConservationLevel（保护级别）

- **类型**：DatatypeProperty
- **Domain**：mmho:MiningAndMetallurgySite（蒋蒙 8-13 未明确，v1.4.7 默认与 hasArea 同 Domain）
- **Range**：xsd:string（枚举值：全国重点 / 省 / 市 / 县级文物保护单位）
- **设计依据**：中国文物保护四级制度（全国/省/市/县级）。
- **comment (en)**：Conservation level under the Chinese cultural heritage protection system, classified into national, provincial, municipal, and county-level.
- **comment (zh)**：文物保护级别，按中国文物保护制度分为全国重点、省级、市级、县级四级。

### 4. 设计决策

- **不挂父属性**：两个新属性都是 mmho 特有语义（CIDOC CRM 无对应），所以不挂 crm 父属性（与 mmho:conductedAtSite / excavatedFrom 等一致）
- **Domain 都是 MiningAndMetallurgySite**：两个属性都是遗址级描述（铜绿山案例驱动）
- **首个 DatatypeProperty**：v1.4.6 之前 0 个 DatatypeProperty（全 59 个 ObjectProperty），v1.4.7 打破这一传统

### 5. 统计变化

| 项 | v1.4.6 | v1.4.7 | 变化 |
|---|---|---|---|
| DatatypeProperty | 0 | 2 | +2 |
| ObjectProperty | 59 | 59 | 0 |
| Class | 117 | 117 | 0 |
| NamedIndividual | 232 | 232 | 0 |
| owl:versionInfo | 1.4.6 | 1.4.7 | — |
| dc:date | 2026-07-22 | 2026-08-14 | — |

### 6. 教训（v1.4.7 新增）

- **DatatypeProperty vs ObjectProperty 决策**：是否用 DatatypeProperty 取决于属性值是否需要关联到 ontology 内部类。如果只需要数值/枚举，用 DatatypeProperty（xsd:decimal / xsd/string）；如果需要关联到 ontology 内的实例（如 E54_Dimension / Condition），用 ObjectProperty
- **铜绿山案例驱动属性设计**：v1.4.7 的两个新属性完全由铜绿山 `mining_site.csv` 的实际字段驱动——这印证了 5.1.2 节"基于实例建模 / 本体参照"的实例化策略
- **蒋蒙工作流程铁律**：8-12 立"不擅自改数据"红线——本体升级严格按"之前讨论过的内容"，不擅自加东西

---



---

## 本体设计守则

1. **不预设具体枚举值**：本体只提供类型容器（Class / meta-type），具体类型由数据建模时根据考古报告语料添加为实例（rdf:type）
2. **类型概念走 `mmho:Type`**：所有"类型概念"都通过 `mmho:Type` 间接继承 `crm:E55_Type`，不直接挂 `crm:E55_Type`
3. **punning Domain 按 CRMarchaeo 标准**：修饰关系本身的属性，Domain 引用 Property IRI（OWL 2 DL 允许）
4. **本体注释不含个人 / 时间戳 / 修订历史**：`rdfs:comment` 只描述设计依据，不含反馈时间、人物名、版本修订叙事

