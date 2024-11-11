这个Markdown文件是关于AlphaFold 3输入文件的说明文档。以下是该文档的中文翻译：

# AlphaFold 3 输入

## 指定输入文件

您可以通过以下两种方式之一向 `run_alphafold.py` 提供输入：

- 单个输入文件：使用 `--json_path` 标志，后跟单个JSON文件的路径。
- 多个输入文件：使用 `--input_dir` 标志，后跟包含JSON文件的目录路径。

## 输入格式

AlphaFold 3 使用自定义的JSON输入格式，与 [AlphaFold 服务器JSON输入格式](https://github.com/google-deepmind/alphafold/tree/main/server) 不同。有关更多信息，请参见[下文](#alphafold服务器json兼容性)。

自定义的AlphaFold 3格式允许：

* 指定蛋白质、RNA和DNA链，包括修饰残基。
* 为蛋白质和RNA链指定自定义的多序列比对（MSA）。
* 为蛋白质链指定自定义的结构模板。
* 使用[化学组分字典（CCD）](https://www.wwpdb.org/data/ccd)代码指定配体。
* 使用SMILES指定配体。
* 通过定义CCD mmCIF格式的配体，并提供[用户自定义CCD](#用户提供的ccd)来指定配体。
* 指定实体之间的共价键。
* 指定多个随机种子。

## AlphaFold 服务器JSON兼容性

[AlphaFold 服务器](https://alphafoldserver.com/)使用一个单独的[JSON格式](https://github.com/google-deepmind/alphafold/tree/main/server)，与此处AlphaFold 3代码库中使用的格式不同。特别是，AlphaFold 3代码库中使用的JSON格式在定义自定义配体、分支糖链和实体之间的共价键方面提供了更多的灵活性和控制。

我们在 `run_alphafold.py` 中提供了一个转换器，它可以自动检测输入JSON格式，在转换器代码中表示为 `dialect`。转换器将AlphaFoldServer JSON表示为 `alphafoldserver`，将此处AlphaFold 3代码库中定义的JSON格式表示为 `alphafold3`。如果检测到的输入JSON格式是 `alphafoldserver`，则转换器将将其转换为JSON格式 `alphafold3`。

### 多个输入

`alphafoldserver` JSON格式的顶层是一个列表，允许在单个JSON中指定多个输入。相比之下，`alphafold3` JSON格式要求每个JSON文件中恰好有一个输入。完全支持在单个 `alphafoldserver` JSON中指定多个输入。

请注意，转换器通过检查JSON的顶层是否为列表来区分 `alphafoldserver` 和 `alphafold3` JSON格式。特别是，如果您传入一个没有顶层列表的 `alphafoldserver` 风格的JSON，那么这被认为是错误的，`run_alphafold.py` 将引发错误。

### 糖链

如果 `alphafoldserver` 格式的JSON指定了糖链，转换器将引发错误。这是因为将 `alphafoldserver` 格式中指定的糖链转换为 `alphafold3` 格式目前不受支持。

### 随机种子

`alphafoldserver` JSON格式允许用户指定 `"modelSeeds": []`，在这种情况下，将为用户随机选择一个种子。另一方面，`alphafold3` 格式要求用户指定一个种子。

转换器将在将 `alphafoldserver` JSON格式转换为 `alphafold3` JSON格式时，如果设置了 `"modelSeeds": []`，则随机选择一个种子。如果在 `alphafoldserver` JSON格式中指定了种子，那么这些种子将在转换为 `alphafold3` JSON格式时保留。

### 离子

虽然AlphaFold服务器在JSON格式中将离子和配体视为不同的实体类型，但AlphaFold 3将离子视为配体。因此，要指定例如镁离子，可以将其指定为类型为 `ligand` 的实体，`ccdCodes: ["MG"]`。

### 序列ID

`alphafold3` JSON格式要求用户为每个实体指定一个唯一标识符（`id`）。另一方面，`alphafoldserver` 不允许为每个实体指定 `id`。因此，转换器会自动分配一个。

转换器遍历 `alphafoldserver` JSON格式中提供的 `sequences` 字段列表，使用以下顺序（“反向电子表格风格”）为每个实体分配一个 `id`：

```
A, B, ..., Z, AA, BA, CA, ..., ZA, AB, BB, CB, ..., ZB, ...
```

对于任何 `count > 1` 的实体，将为实体的每个“副本”任意分配一个 `id`。

## 顶层结构

输入JSON的顶层结构如下：

```json
{
  "name": "作业名称在这里",
  "modelSeeds": [1, 2],  # 至少需要一个种子。
  "sequences": [
    {"protein": {...}},
    {"rna": {...}},
    {"dna": {...}},
    {"ligand": {...}}
  ],
  "bondedAtomPairs": [...],  # 可选
  "userCCD": "...",  # 可选
  "dialect": "alphafold3",  # 必需
  "version": 1  # 必需
}
```

字段指定如下：

*   `name: str`：作业的名称。这个名称的清洁版本用于命名输出文件。
*   `modelSeeds: list[int]`：一个整数随机种子列表。管道和模型将使用列表中的每个种子进行调用。即，如果您提供了 *n* 个随机种子，您将获得 *n* 个预测结构，每个结构都有相应的随机种子。您必须至少提供一个随机种子。
*   `sequences: list[Protein | RNA | DNA | Ligand]`：一系列序列字典，每个字典定义一个分子实体，详见下文。
*   `bondedAtomPairs: list[Bond]`：一个可选的共价键原子列表。这些可以链接实体内部的原子，也可以链接两个实体之间的原子。详见下文。
*   `userCCD: str`：一个可选的字符串，提供用户提供的化学组分字典。这是提供自定义分子的专家模式，当SMILES不足以使用时。当您有一个自定义分子需要与其他实体键合时，也应该使用这个，因为SMILES不能在这种情况下使用，因为它不能提供唯一命名所有原子的可能性。它也可以用来提供参考构象，以防RDKit无法为该配体生成构象。详见下文。
*   `dialect: str`：输入JSON的方言。这必须设置为 `alphafold3`。有关更多信息，请参见 [AlphaFold 服务器JSON兼容性](#alphafold服务器json兼容性)。
*   `version: int`：输入JSON的版本。这必须设置为 1。有关更多信息，请参见 [AlphaFold 服务器JSON兼容性](#alphafold服务器json兼容性)。

## 序列

`sequences` 部分指定蛋白质链、RNA链、DNA链和配体。`sequences` 中的每个实体都必须有一个唯一的ID。ID不需要按字母顺序排序。

### 蛋白质

指定单个蛋白质链。

```json
{
  "protein": {
    "id": "A",
    "sequence": "PVLSCGEWQL",
    "modifications": [
      {"ptmType": "HY3", "ptmPosition": 1},
      {"ptmType": "P1L", "ptmPosition": 5}
    ],
    "unpairedMsa": ...,
    "pairedMsa": ...,
    "templates": [...]
  }
}
```

字段指定如下：

*   `id: str | list[str]`：一个大写字母或多个字母，指定此蛋白质链的每个副本的唯一ID。然后这些ID也用于输出mmCIF文件中。指定ID列表（例如 `["A", "B", "C"]`）意味着具有多个副本的同源链。
*   `sequence: str`：氨基酸序列，指定为使用1个字母标准氨基酸代码的字符串。
*   `modifications: list[ProteinModification]`：一个可选的翻译后修饰列表。每个修饰使用其CCD代码和1个基于残基位置来指定。在上面的例子中，我们看到第一个残基将不是一个脯氨酸（`P`），而是一个 `HY3`。
*   `unpairedMsa: str`：一个可选的此链的多序列比对。这是使用A3M格式（等同于FASTA格式，但也允许使用连字符 `-` 字符表示插入的残基和序列中的间隙）来指定的。详见下文。
*   `pairedMsa: str`：我们建议*不要*使用这个可选字段，并使用 `unpairedMsa` 用于配对目的。详见下文。
*   `templates: list[Template]`：一个可选的结构模板列表。详见下文。

### RNA

指定单个RNA链。

```json
{
  "rna": {
    "id": "A",
    "sequence": "
基索引。映射使用两个相同长度的列表来指定。例如，要表示映射 `{0: 0, 1: 2, 2: 5, 3: 6}`，您需要指定两个索引列表如下：

```json
"queryIndices":    [0, 1, 2, 3],
"templateIndices": [0, 2, 5, 6]
```

您可以提供多个结构模板。请注意，如果提供的mmCIF包含多于一个链，您将获得一个错误，因为无法确定应该使用哪个链作为模板。

## 键

要手动指定共价键，使用 `bondedAtomPairs` 字段。这适用于模拟共价配体，以及定义多CCD配体（例如糖链）。目前不支持在或内部的聚合物实体之间定义共价键。

键被指定为（源原子，目标原子）对，每个原子使用3个字段唯一地址：

*   **实体ID**（`str`）：这对应于该实体的 `id` 字段。
*   **残基ID**（`int`）：这是1-based残基索引*在*链*内*。对于单残基配体，这简单地设置为1。
*   **原子名称**（`str`）：这是给定残基内的唯一原子名称。蛋白质/RNA/DNA残基或CCD配体的原子名称可以在给定化学组分的CCD中查找。这也解释了为什么SMILES配体不支持键：没有可以用来定义键的原子名称。这个缺点可以通过使用用户提供的CCD格式（见下文）来解决。

例如，下面显示了两个键：

```json
"bondedAtomPairs": [
  [["A", 145, "SG"], ["L", 1, "C04"]],
  [["J", 1, "O6"], ["J", 2, "C1"]]
]
```

第一个键是在链A、残基145、原子SG和链L、残基1、原子C04之间。这是一个典型的共价配体示例。第二个键在链J、残基1、原子O6和链J、残基2、原子C1之间。这个键在同一个实体内，是定义糖链的典型示例。

所有键都隐含地假定为共价键。不支持其他键类型。

### 定义糖链

糖链绑定到蛋白质残基上，通常由多个化学组分组成。要定义一个糖链，请定义一个包含糖链所有化学组分的新配体。然后定义一个键，将糖链链接到蛋白质残基上，以及糖链内部各个化学组分之间的所有键。

例如，要定义以下由4个组分（CMP1、CMP2、CMP3、CMP4）组成的糖链，绑定到蛋白质链A中的一个精氨酸上：

```
  ⋮
ALA               CMP4
 |                |
ARG --- CMP1 --- CMP2
 |                |
ALA               CMP3
 ⋮
```

您需要指定：

1. 蛋白质链A。
2. 包含4个组分的配体链B。
3. 键 ARG-CMP1、CMP1-CMP2、CMP2-CMP3、CMP2-CMP4。

## 用户提供的CCD

有两种方法可以模拟在CCD中未定义的自定义配体。如果配体没有与其他实体键合，可以使用[SMILES字符串](https://en.wikipedia.org/wiki/Simplified_Molecular_Input_Line_Entry_System)来定义。否则，需要使用[CCD mmCIF格式](https://www.wwpdb.org/data/ccd#mmcifFormat)来定义该特定配体。

一旦定义，这个配体需要被分配一个不与现有CCD配体名称冲突的名称（例如 `LIG-1`）。避免在名称中使用下划线（`_`），因为这可能会导致mmCIF格式中的问题。

然后可以使用其自定义名称将新定义的配体作为标准CCD配体使用，并且可以使用其命名的原子方案将其与其他实体链接。

### 用户提供的CCD格式

用户提供的CCD必须作为字符串传递给 `userCCD` 字段（在输入JSON的根目录中）。请注意，JSON不允许字符串中的换行符，因此必须使用换行字符（`\n`）来分隔行。字符串也应使用单引号而不是双引号。

使用的主要信息是原子名称和元素，键，以及理想坐标（`pdbx_model_Cartn_{x,y,z}_ideal`），这些本质上作为配体的结构模板，如果RDKit无法为该配体生成构象。

`userCCD` 还可以用来重新定义CCD中的标准化学组分。这在需要重新定义理想坐标时很有用。

以下是一个示例 `userCCD` 重新定义组件X7F，以说明所需的部分。为了可读性，未将换行符替换为 `\n`。

```
data_MY-X7F
#
_chem_comp.id MY-X7F
_chem_comp.name '5,8-bis(oxidanyl)naphthalene-1,4-dione'
_chem_comp.type non-polymer
_chem_comp.formula 'C10 H6 O4'
_chem_comp.mon_nstd_parent_comp_id ?
_chem_comp.pdbx_synonyms ?
_chem_comp.formula_weight 190.152
#
loop_
_chem_comp_atom.comp_id
_chem_comp_atom.atom_id
_chem_comp_atom.alt_atom_id
_chem_comp_atom.type_symbol
_chem_comp_atom.charge
_chem_comp_atom.pdbx_align
_chem_comp_atom.pdbx_aromatic_flag
_chem_comp_atom.pdbx_leaving_atom_flag
_chem_comp_atom.pdbx_stereo_config
_chem_comp_atom.pdbx_backbone_atom_flag
_chem_comp_atom.pdbx_n_terminal_atom_flag
_chem_comp_atom.pdbx_c_terminal_atom_flag
_chem_comp_atom.model_Cartn_x
_chem_comp_atom.model_Cartn_y
_chem_comp_atom.model_Cartn_z
_chem_comp_atom.pdbx_model_Cartn_x_ideal
_chem_comp_atom.pdbx_model_Cartn_y_ideal
_chem_comp_atom.pdbx_model_Cartn_z_ideal
_chem_comp_atom.pdbx_component_atom_id
_chem_comp_atom.pdbx_component_comp_id
_chem_comp_atom.pdbx_ordinal
MY-X7F C02 C1 C 0 1 N N N N N N 48.727 17.090 17.537 -1.418 -1.260 0.018 C02 MY-X7F 1
MY-X7F C03 C2 C 0 1 N N N N N N 47.344 16.691 17.993 -0.665 -2.503 -0.247 C03 MY-X7F 2
MY-X7F C04 C3 C 0 1 N N N N N N 47.166 16.016 19.310 0.677 -2.501 -0.235 C04 MY-X7F 3
MY-X7F C05 C4 C 0 1 N N N N N N 48.363 15.728 20.184 1.421 -1.257 0.043 C05 MY-X7F 4
MY-X7F C06 C5 C 0 1 Y N N N N N 49.790 16.142 19.699 0.706 0.032 0.008 C06 MY-X7F 5
MY-X7F C07 C6 C 0 1 Y N N N N N 49.965 16.791 18.444 -0.706 0.030 -0.004 C07 MY-X7F 6
MY-X7F C08 C7 C 0 1 Y N N N N N 51.249 17.162 18.023 -1.397 1.240 -0.037 C08 MY-X7F 7
MY-X7F C10 C8 C 0 1 Y N N N N N 52.359 16.893 18.837 -0.685 2.443 -0.057 C10 MY-X7F 8
MY-X7F C11 C9 C 0 1 Y N N N N N 52.184 16.247 20.090 0.679 2.445 -0.045 C11 MY-X7F 9
MY-X7F C12 C10 C 0 1 Y N N N N N 50.899 15.876 20.515 1.394 1.243 -0.013 C12 MY-X7F 10
MY-X7F O01 O1 O 0 1 N N N N N N 48.876 17.630 16.492 -
2.611 -1.301 0.247 O01 MY-X7F 11
MY-X7F O09 O2 O 0 1 N N N N N N 51.423 17.798 16.789 -2.752 1.249 -0.049 O09 MY-X7F 12
MY-X7F O13 O3 O 0 1 N N N N N N 50.710 15.236 21.750 2.750 1.257 -0.001 O13 MY-X7F 13
MY-X7F O14 O4 O 0 1 N N N N N N 48.229 15.189 21.234 2.609 -1.294 0.298 O14 MY-X7F 14
MY-X7F H1 H1 H 0 1 N N N N N N 46.487 16.894 17.367 -1.199 -3.419 -0.452 H1 MY-X7F 15
MY-X7F H2 H2 H 0 1 N N N N N N 46.178 15.732 19.640 1.216 -3.416 -0.429 H2 MY-X7F 16
MY-X7F H3 H3 H 0 1 N N N N N N 53.348 17.177 18.511 -1.221 3.381 -0.082 H3 MY-X7F 17
MY-X7F H4 H4 H 0 1 N N N N N N 53.040 16.041 20.716 1.212 3.384 -0.062 H4 MY-X7F 18
MY-X7F H5 H5 H 0 1 N N N N N N 50.579 17.904 16.365 -3.154 1.271 0.830 H5 MY-X7F 19
MY-X7F H6 H6 H 0 1 N N N N N N 49.785 15.059 21.877 3.151 1.241 -0.880 H6 MY-X7F 20
#
loop_
_chem_comp_bond.comp_id
_chem_comp_bond.atom_id_1
_chem_comp_bond.atom_id_2
_chem_comp_bond.value_order
_chem_comp_bond.pdbx_aromatic_flag
_chem_comp_bond.pdbx_stereo_config
_chem_comp_bond.pdbx_ordinal
MY-X7F O01 C02 DOUB N N 1
MY-X7F O09 C08 SING N N 2
MY-X7F C02 C03 SING N N 3
MY-X7F C02 C07 SING N N 4
MY-X7F C03 C04 DOUB N N 5
MY-X7F C08 C07 DOUB Y N 6
MY-X7F C08 C10 SING Y N 7
MY-X7F C07 C06 SING Y N 8
MY-X7F C10 C11 DOUB Y N 9
MY-X7F C04 C05 SING N N 10
MY-X7F C06 C05 SING N N 11
MY-X7F C06 C12 DOUB Y N 12
MY-X7F C11 C12 SING Y N 13
MY-X7F C05 O14 DOUB N N 14
MY-X7F C12 O13 SING N N 15
MY-X7F C03 H1 SING N N 16
MY-X7F C04 H2 SING N N 17
MY-X7F C10 H3 SING N N 18
MY-X7F C11 H4 SING N N 19
MY-X7F O09 H5 SING N N 20
MY-X7F O13 H6 SING N N 21
#
_pdbx_chem_comp_descriptor.type SMILES_CANONICAL
_pdbx_chem_comp_descriptor.descriptor 'Oc1ccc(O)c2C(=O)C=CC(=O)c12'
#
```

## 完整示例

下面提供了一个示例，展示了输入格式的所有方面。请注意，AlphaFold 3不会直接运行此输入，因为它省略了某些字段，且序列没有生物学意义。

```json
{
  "name": "Hello fold",
  "modelSeeds": [10, 42],
  "sequences": [
    {
      "protein": {
        "id": "A",
        "sequence": "PVLSCGEWQL",
        "modifications": [
          {"ptmType": "HY3", "ptmPosition": 1},
          {"ptmType": "P1L", "ptmPosition": 5}
        ],
        "unpairedMsa": ...,
      }
    },
    {
      "protein": {
        "id": "B",
        "sequence": "RPACQLW",
        "templates": [
          {
            "mmcif": ...,
            "queryIndices": [0, 1, 2, 4, 5, 6],
            "templateIndices": [0, 1, 2, 3, 4, 8]
          }
        ]
      }
    },
    {
      "dna": {
        "id": "C",
        "sequence": "GACCTCT",
        "modifications": [
          {"modificationType": "6OG", "basePosition": 1},
          {"modificationType": "6MA", "basePosition": 2}
        ]
      }
    },
    {
      "rna": {
        "id": "E",
        "sequence": "AGCU",
        "modifications": [
          {"modificationType": "2MG", "basePosition": 1},
          {"modificationType": "5MC", "basePosition": 4}
        ],
        "unpairedMsa": ...
      }
    },
    {
      "ligand": {
        "id": ["F", "G", "H"],
        "ccdCodes": ["ATP"]
      }
    },
    {
      "ligand": {
        "id": "I",
        "ccdCodes": ["NAG", "FUC"]
      }
    },
    {
      "ligand": {
        "id": "Z",
        "smiles": "CC(=O)OC1C[NH+]2CCC1CC2"
      }
    }
  ],
  "bondedAtomPairs": [
    [["A", 1, "CA"], ["B", 1, "CA"]],
    [["A", 1, "CA"], ["G", 1, "CHA"]],
    [["J", 1, "O6"], ["J", 2, "C1"]]
  ],
  "userCcd": ...,
  "dialect": "alphafold3",
  "version": 1
}
```

请注意，由于文档篇幅较长，我已经将其分成两部分进行翻译。这是第一部分的翻译。如果您需要剩余部分的翻译，请告知。
