# MMHO Changelog


## [1.4.8] - 2026-08-24

### 改动
- **mmho:hasDimension** 的 
dfs:domain 从 mmho:ExcavatedArtifacts 扩展为 unionOf(mmho:ExcavatedArtifacts, mmho:AnthropicFeature)
- 注释更新：英文 "Describes the dimensions of an artifact" → "Describes the dimensions of an artifact or an anthropic feature"；中文 "遗物具有尺寸测量值" → "遗物或遗迹具有尺寸测量值"

### 理由
- mmho v1.4.7 设计时 hasDimension 只挂在 ExcavatedArtifacts 上，但矿冶实例化中"遗迹"的尺寸（高程/深度/间距）有独立价值（铜绿山 AnthropicFeature 4 类尺寸 484 条）
- 通过 unionOf 扩展，**mmho:hasDimension 现在同时支持 ExcavatedArtifacts 和 AnthropicFeature**
- 与 CIDOC CRM 子属性层级一致（crm:P43_has_dimension 的 domain 是 crm:E70 Thing）

### 迁移
- Neo4j **不需要重新导入**
- 铜绿山 484 个已挂在 AnthropicFeature 上的 hasDimension 关系**自动合规**（schema 升级使原本不合规变为合规）
- 孝民屯 181 个已挂在 ExcavatedArtifacts 上的 hasDimension 关系**保持不变**

## [1.5.0] - 2026-09-03

### 改动
- **mmho:hasVisualFeature** 的 rdfs:domain 从 mmho:SmeltingProducts 改为 crm:E18_Physical_Thing
- **mmho:hasCondition** 的 rdfs:domain 从 mmho:SmeltingProducts 改为 crm:E18_Physical_Thing
- 中文注释更新：从"冶炼产品具有的视觉特征"改为"物理事物（遗物或冶炼产品）具有的视觉特征"

### 理由
- 孝民屯知识图谱实例化中，铸范（ExcavatedArtifacts）上的纹饰信息非常重要（57 件陶范有纹饰），但 v1.4.9 的 hasVisualFeature 只能挂在 SmeltingProducts 上
- 陶范也可能存在保存状况（烟炱、铜渣等），需要 hasCondition 也能挂在 ExcavatedArtifacts 上
- 通过扩展到 crm:E18_Physical_Thing，覆盖所有物理事物子类（包括 ExcavatedArtifacts 和 SmeltingProducts）
- 与 CIDOC CRM 一致：crm:P65_shows_visual_item 和 crm:P44_has_condition 的 domain 都是 crm:E18_Physical_Thing

### 迁移
- Neo4j 不需要重新导入
- 孝民屯 42 条陶范上的 hasVisualFeature 关系自动合规
- 已有的 SmeltingProducts 上的 hasVisualFeature/hasCondition 关系保持不变
- 实例化新增6 个 NamedIndividual（4 个 Inscription 子类 + 2 个 Pattern 子类）

### 新增实例（仅在知识图谱，未进本体）
- Pattern: RoundEddyPattern (圆涡纹), VerticalRidgePattern (直棱纹)
- Inscription: CarvedInscription (刻字铭文), YaoShuInscription (筮数易卦铭文), YangwenInscription (阳文铭文), DivinationInscription (占卜铭文)


## v1.6.0 (2026-09-07) — schema 一致性修复 + 矿保护级别/面积 ObjectProperty 化

### 变更

#### 1. 修复 OWL 不一致
- **删除** hasFunctionalSpatialRelation 在 L1918 的 <owl:Class> 重复声明 (保留 L369 ObjectProperty)
- 原因: 同一 IRI 不能既是 ObjectProperty 又是 Class, owlready2/HermiT/Pellet 报 "belongs to more than one entity types"
- punning Domain 设计 (hasFunctionalSpatialRelationType rdfs:domain ...hasFunctionalSpatialRelation) 仍然有效, 因为属性 IRI 仍存在

#### 2. 改 hasConservationLevel / hasArea 为 ObjectProperty
- **改 hasConservationLevel**: DatatypeProperty (xsd:string) → ObjectProperty (Range=ConservationLevel)
- **改 hasArea**: DatatypeProperty (xsd:string) → ObjectProperty (Range=Area)
- hasConservationLevel 映射: crm:P2_has_type

#### 3. 新建 ConservationLevel 类 + 4 实例
- **类** ConservationLevel (subClassOf crm:E55_Type, 中文"文物保护级别")
- **实例**: NationalLevel / ProvincialLevel / MunicipalLevel / CountyLevel (四级保护制度)

#### 4. 新建 Area 类 (复用继承
- **类** Area (subClassOf crm:E54_Dimension)
- 通过 hasDimensionValue (→DimensionValue) + hasDimensionUnit (→DimensionMeasurementUnit) 关联
- 复用 ArtifactDimension 的尺寸值/单位框架

#### 5. ArtifactDimension 类改名 DimensionInformation
- **IRI**: mmho:ArtifactDimension → mmho:DimensionInformation
- **中文 Label**: "遗物尺寸" → "尺寸信息"
- **英文 Label**: "Artifact Dimension" → "Dimension Information"
- **注释**: 改Domain 适用范围 (遗迹+遗物)
- hasDimension / hasDimensionUnit / hasDimensionType 的 Domain/Range 全部跟随改名

#### 6. 新建 hasDimensionValue 属性
- **属性**: ObjectProperty (Domain=DimensionInformation, Range=DimensionValue, subPropertyOf crm:P90_has_value)
- **注释**: 注: 数据库现有 hasDimensionNumber 关系跟本属性语义等价, 保持向后兼容

### 验证
- owlready2 加载: OK (不再报错)
- Neo4j 21 条 query: 21/21 通过 (数据零影响)

### 备份
- ontology_history/MMHO_v1.5.0_pre_v15fix_20260907.owl

## v1.6.0 patch (2026-09-07) — 二次修复

### 变更

#### 1. 删除 mmho:hasDimensionValue 属性 (重复定义)
- **原因**: 本体里已有 mmho:hasDimensionNumber 属性 (蒋蒙之前定义, 数据库沿用), 新建 hasDimensionValue 是重复
- **动作**: 删除 mmho:hasDimensionValue 整个 ObjectProperty 块
- **保留**: mmho:hasDimensionNumber (与数据库已有 1071075 条关系一一对应)

#### 2. 改 mmho:hasDimensionNumber Domain: ArtifactDimension → DimensionInformation
- 跟随 DimensionInformation 改名同步

#### 3. 改 mmho:hasDimensionType Domain: ArtifactDimension → DimensionInformation
- 跟随 DimensionInformation 改名同步

#### 4. 修复 L1005 注释残留 (DimensionInformation 前的旧注释)
- **原**: <!-- http://w3id.org/mmho#ArtifactDimension -->
- **新**: <!-- http://w3id.org/mmho#DimensionInformation -->

### 验证
- owlready2 加载: OK
- Neo4j 21 条 query: 21/21 通过
- ArtifactDimension 字符串本体里 0 处残留

### 备份
- ontology_history/MMHO_v1.6.0_pre_v15fix2_20260907.owl
