# historico-faturamento

![Python](https://img.shields.io/badge/Python-3.13.3-blue)
![pandas](https://img.shields.io/badge/pandas-2.1.1-orange)
![openpyxl](https://img.shields.io/badge/openpyxl-3.1.2-red)
![glob](https://img.shields.io/badge/glob-builtin-lightgrey)

---

## Table of Contents
- [English](#english)
  - [Overview](#overview)
  - [Source of Input Data](#source-of-input-data)
  - [Folder Naming Guide: Input and Output](#folder-naming-guide-input-and-output)
  - [Why Naming Conventions Matter](#why-naming-conventions-matter)
  - [Adding New Months](#adding-new-months)
  - [Scripts](#scripts)
- [Português](#português)
  - [Visão Geral](#visão-geral)
  - [Origem dos Dados](#origem-dos-dados)
  - [Estrutura das Pastas](#estrutura-das-pastas)
  - [Padrões de Nomeação](#padrões-de-nomeação)
  - [Por Que a Nomeação é Importante](#por-que-a-nomeação-é-importante)
  - [Adicionando Novos Meses](#adicionando-novos-meses)
  - [Scripts](#scripts-1)

---

## English

### Overview
Repository for the billing history of **ISM GOMES DE MATTOS**, covering the years **2024** and **2025**.  

This is a **public version** of the README, with all sensitive data removed.  
All mentions of units or billing data are for educational purposes only.

The billing includes:
- All **UFC** RUs in Fortaleza  
- The **HGF** (Hospital Geral de Fortaleza)  
- The **IJF** (Instituto Doutor José Frota)  
- Several **UPAs**  
- The **UFPB** (Federal University of Paraíba)  
- The **UNB** (University of Brasília)  
- And approximately *33 more* units, mostly federal hospitals and universities.

The project reads and consolidates multiple data tables into a standardized format, classifies them, and corrects inconsistencies.

---

### Source of Input Data

Data originates from billing exports, with one definitive file per unit and period.

#### Example Reference Table
```
C:\historico-faturamento\Planning\example_File\tabela_parsing_df-v3.xlsx
```

#### Classification Table (Python Example)
```python
try:
    classf_Tab_Dir = glob.glob("C:\\*\\*\\HRN\\Classf\\*")
    print(str(classf_Tab_Dir[0]))
    classfTab = pd.read_excel(f"{str(classf_Tab_Dir[0])}")
except (IndexError, OSError):
    raise OSError("Classification table not found. Check your directory structure.")
```

#### Example Input File
```
CR_HRN_2023_15_12_2024_14_1.xlsx
```

---

### Folder Naming Guide: `Input` and `Output`

#### Folder Structure Example
```
historico-faturamento/
├── Input/
│   └── HRN/
│       └── 2024/
│           └── 1-2023_15_12_2024_14_1/
│               └── CR_HRN_2023_15_12_2024_14_1.xlsx
├── Output/
│   └── HRN_output.xlsx
└── Planning/
    └── example_File/
        └── tabela_parsing_df-v3.xlsx
```

> [!CAUTION]
> Keep this structure consistent — otherwise scripts won’t locate the classification table.

#### Example Paths
Input:
```
C:\historico-faturamento\Input\HRN\2024\1-2023_15_12_2024_14_1\CR_HRN_2023_15_12_2024_14_1.xlsx
```

Output:
```
C:\historico-faturamento\Output\HRN\HRN_output.xlsx
```

#### Base Directory
```
C:\historico-faturamento
```

#### Input and Output Behavior
- `\Input`: manually created  
- `\Output`: only the root folder must exist — subfolders are auto-generated

```python
output_base_dir = r"C:\historico-faturamento\Output\HRN"
os.makedirs(output_base_dir, exist_ok=True)
```

#### Competence Folder Naming Example
```
1-2023_15_12_2024_14_1
```
- Month: `1-`  
- Start Date: `2023_15_12`  
- End Date: `2024_14_1`

#### File Naming Convention
```
CR_HRN_2023_15_12_2024_14_1.xlsx
```
- **CR** → File classification  
- **HRN** → Unit  
- **2023_15_12** → Start date  
- **2024_14_1** → End date  
- **.xlsx** → File extension

---

### Why Naming Conventions Matter

The scripts extract metadata from **filenames rather than the document contents**.  
This is because the information inside the files is often **inconsistent or missing**, and using filenames ensures a **standardized, reliable reference** for all processing.

Example reference table construction:
```python
tab = pd.DataFrame(dados_arquivos)
tab["nome_arquivo"] = arquivos
tab.loc[:,6] = tab.loc[:,6].astype(int)
tab.loc[:,9] = tab.loc[:,9].astype(int)
selectedrows = tab[tab[5] == "2025"].sort_values(by=6)
tab = tab[tab[5] == "2024"].sort_values(by=6)
tab = pd.concat([tab, selectedrows], axis=0)
```

These names determine:
- Chronological order  
- Competence period assignment  
- Iteration management (`tabIteration`)  

Example of extracting competence:
```python
comp_inicio = f"{tab.iloc[int(tabIteration), 16]}/{yearMonths[(int(tab.iloc[tabIteration, 17])-1)].upper()} de {tab.iloc[tabIteration, 15]}"
comp_fim = f"{tab.iloc[int(tabIteration), 19]}/{yearMonths[(int(tab.iloc[tabIteration, 20])-1)].upper()} de {tab.iloc[tabIteration, 18]}"
```

---

### Adding New Months

To add a new competence period:
1. Create the folder for the new month (and year if needed).  
2. Place the new `.xlsx` input file inside.  
3. Follow naming conventions.  
4. Run the script and review output.

---

### Scripts

Each unit has a dedicated Python script due to structural differences between tables.  
Simpler units process quickly; others require complex parsing and validation logic.

---

## Português

### Visão Geral
Repositório para histórico de faturamento da **ISM GOMES DE MATTOS**, referente aos anos **2024** e **2025**.  

Versão **pública** do README, sem qualquer dado confidencial.  
Todas as menções a unidades ou faturamento têm finalidade **educativa**.

O projeto lê várias tabelas, consolida em um formato padronizado, classifica e corrige inconsistências.

Inclui:
- Todos os RUs da **UFC** em Fortaleza  
- O **HGF** (Hospital Geral de Fortaleza)  
- O **IJF** (Instituto Doutor José Frota)  
- Diversas **UPAs**  
- **UFPB** e **UNB**  
- E aproximadamente *33 outras* unidades, principalmente hospitais e universidades federais

---

### Origem dos Dados

Os dados provêm de exportações do sistema de faturamento, com um arquivo definitivo por unidade e período.

#### Exemplo de Tabela de Referência
```
C:\historico-faturamento\Planning\example_File\tabela_parsing_df-v3.xlsx
```

#### Exemplo de Arquivo de Classificação
```python
try:
    classf_Tab_Dir = glob.glob("C:\\*\\*\\HRN\\Classf\\*")
    print(str(classf_Tab_Dir[0]))
    classfTab = pd.read_excel(f"{str(classf_Tab_Dir[0])}")
except (IndexError, OSError):
    raise OSError("Tabela de classificação não encontrada. Verifique a estrutura de pastas.")
```

#### Exemplo de Arquivo de Entrada
```
CR_HRN_2023_15_12_2024_14_1.xlsx
```

---

### Estrutura das Pastas
```
historico-faturamento/
├── Input/
│   └── HRN/
│       └── 2024/
│           └── 1-2023_15_12_2024_14_1/
│               └── CR_HRN_2023_15_12_2024_14_1.xlsx
├── Output/
│   └── HRN_output.xlsx
└── Planning/
    └── example_File/
        └── tabela_parsing_df-v3.xlsx
```

> [!CAUTION]
> Mantenha a estrutura de pastas consistente — caso contrário, os scripts não localizarão a tabela de classificação.

---

### Padrões de Nomeação

- Evite zeros à esquerda nas datas (`2024_1_2`, não `2024_01_02`)  
- Mantenha os nomes de pastas e arquivos consistentes

#### Pasta de Competência
```
1-2023_15_12_2024_14_1
```
- Mês: `1-`  
- Início: `2023_15_12`  
- Fim: `2024_14_1`

#### Arquivo de Entrada
```
CR_HRN_2023_15_12_2024_14_1.xlsx
```
- **CR** → Classificação  
- **HRN** → Unidade  
- **2023_15_12** → Data início  
- **2024_14_1** → Data fim  
- **.xlsx** → Extensão

---

### Por Que a Nomeação é Importante

Os scripts extraem metadados **dos nomes dos arquivos e não do conteúdo das planilhas**, porque a informação dentro dos arquivos muitas vezes é **inconsistente ou ausente**.  
Isso garante **padronização e confiabilidade** durante todo o processamento.

Exemplo de construção da tabela de referência:
```python
tab = pd.DataFrame(dados_arquivos)
tab["nome_arquivo"] = arquivos
tab.loc[:,6] = tab.loc[:,6].astype(int)
tab.loc[:,9] = tab.loc[:,9].astype(int)
selectedrows = tab[tab[5] == "2025"].sort_values(by=6)
tab = tab[tab[5] == "2024"].sort_values(by=6)
tab = pd.concat([tab, selectedrows], axis=0)
```

Define:
- Ordem cronológica  
- Atribuição de competência  
- Controle de iteração (`tabIteration`)  

Exemplo de extração de competência:
```python
comp_inicio = f"{tab.iloc[int(tabIteration), 16]}/{yearMonths[(int(tab.iloc[tabIteration, 17])-1)].upper()} de {tab.iloc[tabIteration, 15]}"
comp_fim = f"{tab.iloc[int(tabIteration), 19]}/{yearMonths[(int(tab.iloc[tabIteration




