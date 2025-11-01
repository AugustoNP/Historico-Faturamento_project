# historico-faturamento

Repositório para histórico de faturamento da ISM GOMES DE MATTOS, anos 2024 e 2025.

Essa é uma versão pública do README, sem detalhes internos sobre o faturamento da empresa.

Qualquer menção de unidade/faturamento tem o intuito de educar e explicar o projeto para quem tiver interesse. 


## Origem dos dados de entrada
São recebidos do faturamento, com apenas um arquivo definitivo para um certo período de tempo e unidade, podendo ter mais de um por competência.

### Tabelas Recebidas do Faturamento

Temos a tabela de exemplo : `("C:\\historico-faturamento\\Planning\\example_File\\tabela_parsing_df-v3.xlsx")`\
A tabela de classificação : 
```python
# Ler tabela de classificação 
try:
    
    classf_Tab_Dir = (glob.glob("C:\\*\\*\\HRN\\Classf\\*"))
    try:
        print(str(classf_Tab_Dir[0]))
        classfTab = pd.read_excel(f"{str(classf_Tab_Dir[0])}")
    except IndexError:
        raise IndexError (("O .glob não retornou uma lista válida,provável problema de diretório."))

except OSError:
    
    os.system("cls")
    raise OSError(("Não foi possível ler a tabela de classificação.\nVerifique se ela está presente no diretório de seu computador."))
```
E a tabela de input em si, o exemplo que estamos usando é `CR_HRN_2023_15_12_2024_14_1.xlsx`

# Guia de nomes da pasta `Input` e `Output`
Um breve guia da importância de nomear as pastas de input corretamente, e como o script salva arquivos.

## Tabelas Padrão/Modelo
Todos os scripts pegam o mesmo arquivo com a filepath `"C:\historico-faturamento\Planning\example_File\tabela_parsing_df-v3.xlsx"`\
> [!CAUTION]
>**_Prioridade_** manter essa mesma estrutura de pastas, senão o script **_não vai conseguir_** achar o arquivo de classificação!!!


### Exemplo de filepaths
A filepath do input é algo assim : `"C:\historico-faturamento\Input\HRN\2024\1-2023_15_12_2024_14_1\CR_HRN_2023_15_12_2024_14_1.xlsx"`

E do output : `"C:\historico-faturamento\Output\HRN\HRN_output.xlsx"`

Agora vamos destrinchar isso parte por parte.

### O nosso diretório onde estamos mantendo todas as pastas do do projeto : `C:\historico-faturamento` 
> [!WARNING]
> Se possível, nomear do mesmo jeito desse exemplo. Caso contrário, pode causar erros de referência em certas unidades.

### Output são os arquivos de saída, Input são os de entrada. : `\Output ou \Input`
Todas do `\Input` tem que ser criadas manualmente, seguindo a estrutura de exemplo mais abaixo.\
A única pasta no output que tem que ser criada manualmente é a `\Output` em si.\
Cada script cria sua própria pasta na hora de salvar, contanto que a pasta `\Output` exista no lugar correto.
```python
output_base_dir = r"C:\historico-faturamento\Output\HRN"
try:
    os.makedirs(output_base_dir, exist_ok=False)
    print(f"Foi criada a pasta: {output_base_dir}")
except FileExistsError:
    print(f"A pasta: {output_base_dir} já existe!")
```
que nem esse bloco aqui.


### A unidade : `\HRN`
Aqui é nossa unidade, no exemplo utilizado é HRN.

### O ano : `\2024`
O ano de nosso input, contendo normalmente 12 pastas de competência.

### A pasta de competência : `\1-2023_15_12_2024_14_1`

**Ela é dividida em _três_ partes:**
#### O mês : `1-`
Tem doze dentro da pasta de ano, é separado do resto dos títulos de competência por um "-"
#### O início da competência : `2023_15_12`
> [!WARNING]
>(tem um "_" separando eles, muito importante.)
#### E o fim da competência : `2024_14_1`
Tem uma estrutura YYYY-DD-MM./
> [!CAUTION]
> Não pode ter zero como o primeiro número da string, por exemplo,\
A data "1/2/2024" NÃO PODE SER "01/02/2024", ou no caso "2024_1_2" não pode ser "2024_01_02".\
Todos os zeros que começam datas tem que ser removidos, o correto seria : "2024_1_2", seguindo o YYYY-DD-MM.  
Isso é muito importante para não terem erros na hora da criação da tabela de referência, que é utilizada para muitas coisas na atribuição. 


###  O nome do arquivo em si. : `\CR_HRN_2023_15_12_2024_14_1.xlsx`

**Ele é dividido em _quatro_ partes:**
#### A classificação do arquivo de input : `\CR`
#### A unidade : `_HRN`
#### O início da competência : `2023_15_12`
(tem um "_" separando eles, muito importante.)
#### E o fim da competência : `2024_14_1`
A estrutura de competência é a mesma que as da pasta de competência, nesse caso são iguais.\
Porém, há competências cujo faturamento é dividido em duas partes, com mais de um arquivo dentro de uma mesma competência.

#### O tipo de arquivo : `.xlsx`

## Porquê nomear os arquivos dessa maneira é tão importante?

### A tabela de referência.
Todo script inicia pegando os arquivos de input e definindo uma **"Tabela de _referência_"**
```python
tab = pd.DataFrame(dados_arquivos)
tab["nome_arquivo"] = arquivos
tab.loc[:,6] = tab.loc[:,6].astype(int)
tab.loc[:,9] = tab.loc[:,9].astype(int)
selectedrows = tab[tab[5] == "2025" ]
selectedrows = selectedrows.sort_values(by = 6)
tab = tab[tab[5] == "2024" ]
tab = tab.sort_values(by = 6)
tab = (pd.concat([tab, selectedrows], axis=0))
```
O bloco acima cria a tabela de referência e classifica ela em ordem cronológica.

Essa tabela contém todas as informações que tem no nosso filepath.\
A tabela dessa série de arquivos :`"C:\historico-faturamento\Input\HRN\**\**\**"` seria algo assim:

0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20 | 21 | nome_arquivo
--|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|----|----|----|----|----|----|------------
0 | C: | historico | faturamento | Input | HRN | 2024 | 1 | 2023 | 15 | 12 | 2024 | 14 | 1 | CF | HRN | 2023 | 15 | 12 | 2024 | 14 | 1 | C:\historico-faturamento\Input\HRN\2024\1-2023...
1 | C: | historico | faturamento | Input | HRN | 2024 | 2 | 2024 | 15 | 1 | 2024 | 14 | 2 | CF | HRN | 2024 | 15 | 1 | 2024 | 14 | 2 | C:\historico-faturamento\Input\HRN\2024\2-2024...
2 | C: | historico | faturamento | Input | HRN | 2024 | 3 | 2024 | 15 | 2 | 2024 | 14 | 3 | CF | HRN | 2024 | 15 | 2 | 2024 | 14 | 3 | C:\historico-faturamento\Input\HRN\2024\3-2024...
3 | C: | historico | faturamento | Input | HRN | 2024 | 4 | 2024 | 15 | 3 | 2024 | 14 | 4 | CF | HRN | 2024 | 15 | 3 | 2024 | 14 | 4 | C:\historico-faturamento\Input\HRN\2024\4-2024...
4 | C: | historico | faturamento | Input | HRN | 2024 | 5 | 2024 | 15 | 4 | 2024 | 14 | 5 | CF | HRN | 2024 | 15 | 4 | 2024 | 14 | 5 | C:\historico-faturamento\Input\HRN\2024\5-2024...
5 | C: | historico | faturamento | Input | HRN | 2024 | 6 | 2024 | 15 | 5 | 2024 | 14 | 6 | CF | HRN | 2024 | 15 | 5 | 2024 | 14 | 6 | C:\historico-faturamento\Input\HRN\2024\6-2024...
6 | C: | historico | faturamento | Input | HRN | 2024 | 7 | 2024 | 15 | 6 | 2024 | 14 | 7 | CF | HRN | 2024 | 15 | 6 | 2024 | 14 | 7 | C:\historico-faturamento\Input\HRN\2024\7-2024...
7 | C: | historico | faturamento | Input | HRN | 2024 | 8 | 2024 | 15 | 7 | 2024 | 14 | 8 | CF | HRN | 2024 | 15 | 7 | 2024 | 14 | 8 | C:\historico-faturamento\Input\HRN\2024\8-2024...
8 | C: | historico | faturamento | Input | HRN | 2024 | 9 | 2024 | 15 | 8 | 2024 | 14 | 9 | CF | HRN | 2024 | 15 | 8 | 2024 | 14 | 9 | C:\historico-faturamento\Input\HRN\2024\9-2024...
9 | C: | historico | faturamento | Input | HRN | 2024 | 10 | 2024 | 15 | 9 | 2024 | 14 | 10 | CF | HRN | 2024 | 15 | 9 | 2024 | 14 | 10 | C:\historico-faturamento\Input\HRN\2024\10-202...
10 | C: | historico | faturamento | Input | HRN | 2024 | 11 | 2024 | 15 | 10 | 2024 | 14 | 11 | CF | HRN | 2024 | 15 | 10 | 2024 | 14 | 11 | C:\historico-faturamento\Input\HRN\2024\11-202...
11 | C: | historico | faturamento | Input | HRN | 2024 | 12 | 2024 | 15 | 11 | 2024 | 14 | 12 | CF | HRN | 2024 | 15 | 11 | 2024 | 31 | 12 | C:\historico-faturamento\Input\HRN\2024\12-202...
12 | C: | historico | faturamento | Input | HRN | 2025 | 1 | 2024 | 15 | 12 | 2025 | 14 | 1 | CF | HRN | 2024 | 15 | 12 | 2025 | 14 | 1 | C:\historico-faturamento\Input\HRN\2025\1-2024...
13 | C: | historico | faturamento | Input | HRN | 2025 | 2 | 2025 | 15 | 1 | 2025 | 14 | 2 | CF | HRN | 2025 | 15 | 1 | 2025 | 14 | 2 | C:\historico-faturamento\Input\HRN\2025\2-2025...
14 | C: | historico | faturamento | Input | HRN | 2025 | 3 | 2025 | 15 | 2 | 2025 | 14 | 3 | CF | HRN | 2025 | 15 | 2 | 2025 | 14 | 3 | C:\historico-faturamento\Input\HRN\2025\3-2025...
15 | C: | historico | faturamento | Input | HRN | 2025 | 4 | 2025 | 15 | 3 | 2025 | 14 | 4 | CF | HRN | 2025 | 15 | 3 | 2025 | 3 | 4 | C:\historico-faturamento\Input\HRN\2025\4-2025...
16 | C: | historico | faturamento | Input | HRN | 2025 | 4 | 2025 | 15 | 3 | 2025 | 14 | 4 | CF | HRN | 2025 | 4 | 4 | 2025 | 14 | 4 | C:\historico-faturamento\Input\HRN\2025\4-2025...

Como pode ver, ela inclui todos os arquivos que são `HRN` e estão no `Input`.\
Se tiver algum erro nos nomes do arquivos, vai ter erros no output.

#### O que é afetado pela tabela de competência.

A estrutura do código é basicamente assim dependendo da unidade:

```
Definição de arquivos de input e de classificação.

    tabiteration += 1
    loop de arquivos
        Determinar coisas como o nome do arquivo de output, etc.
        Filtrar e ler abas que são únicas, caso necessário
        
        loop de abas
            Fazer toda atribuição, a lógica muda muito dependendo do tipo de tabela,\
            de vez em quando o loop de abas nem necessário é, pode ser utilizado apenas o de arquivos com atribuições diretas.
    
    tabiteration += 1
filtragem final
salvar output
```

`tabiteration += 1` é o nosso contador, a utilizamos ele pra saber qual dos arquivos na lista estamos lendo, assim conseguindo pegar infomações a base de posição usando o .iloc.\
Sendo assim, A competência é definida desse jeito dentro do nosso código :
```python
        comp_inicio = f"{tab.iloc[int(tabIteration), 16]}/{yearMonths[(int(tab.iloc[tabIteration, 17])-1)].upper()} de {tab.iloc[tabIteration, 15]}"
        comp_fim = f"{tab.iloc[int(tabIteration), 19]}/{yearMonths[(int(tab.iloc[tabIteration, 20])-1)].upper()} de {tab.iloc[tabIteration, 18]}"
``` 
Usamos nossa 
> tabiteration 

Para saber qual dos arquivos estamos lendo, e depois pegamos as informações necessárias da tabela de classificação.\
E não é só utilizada pra definir a competência.
É um ponto de referência que é utilizado por todo o script, por isso é tão importante que seu input **_(o nome dos seus arquivos!!!)_** seja coerente.
#### Como adicionar novos meses?
É só criar uma nova pasta de competência, e ano se for necessário, e depois renomear o arquivo de input com a estrutura que já foi explicada.

Então, vamos ter de exemplo a pasta do **HRN/2025** no momento que estou escrevendo esse readme:

<img src="Img\image-2.png" alt="Alt Text" style="width:70%; height:auto;">\
Como podem ver, só existem pastas até o mês de abril.

> [!NOTE]
> As pastas podem ser criadas manualmente, mas no filepath C:\historico-faturamento\Planning\CompfileStrucuture\2025 tem uns exemplos de pastas com o estilo de competência do HRN.\
**(As pastas comp são as competências estilo universidade, podem ser _reutilizadas_ pra ganhar tempo na hora de criar a estrutura de seu novo arquivo.)**<br/>


<img src="Img\image-3.png" alt="Alt Text" style="width:70%; height:auto;">\

Após criar ou copiar a pasta de competência, ela deve ser algo assim:<br/>

<img src="Img\image-4.png" alt="Alt Text" style="width:70%; height:auto;">\



Pegamos nosso arquivo de input, e colocamos dentro da pasta,<br/>

<img src="Img\image-6.png" alt="Alt Text" style="width:70%; height:auto;">\



Aí renomeamos para seguir a estrutura.<br/>

<img src="Img\image-5.png" alt="Alt Text" style="width:70%; height:auto;">\

Depois é só rodar o script e esperar.

O terminal :

<img src="Img\image-7.png" alt="Alt Text" style="width:70%; height:auto;">\

e aqui está o nosso output final.

<img src="Img\image-8.png" alt="Alt Text" style="width:70%; height:auto;">\




## Scripts
### Como são estruturados os Scripts?
Os scripts são divididos de por unidade, já que todas tem uma estrututra de tabela diferente.\
Por isso, alguns scripts são mais simples e rodam mais rápido, e outros demoram um pouco mais e são mais complicados.




### DEPENDÊNCIAS
#### Python
Python 3.13.3

##### Pkg
pandas 2.2.3

openpyxl 3.1.5

tabula-py 2.10.0

xlrd 2.0.1

numpy 2.2.5

##### Lista completa de pkg
<span style="font-size: 0.5em;">
anyio                   4.9.0
<br>asttokens               3.0.0
<br>certifi                 2025.4.26
<br>colorama                0.4.6
<br>comm                    0.2.2
<br>debugpy                 1.8.15
<br>decorator               5.2.1
<br>defusedxml              0.7.1
<br>distro                  1.9.0
<br>et_xmlfile              2.0.0
<br>executing               2.2.0
<br>fuzzywuzzy              0.18.0
<br>googletrans             4.0.2
<br>h11                     0.16.0
<br>h2                      4.2.0
<br>hpack                   4.1.0
<br>httpcore                1.0.9
<br>httpx                   0.28.1
<br>hyperframe              6.1.0
<br>idna                    3.10
<br>ipykernel               6.29.5
<br>ipython                 9.4.0
<br>ipython_pygments_lexers 1.1.1
<br>jedi                    0.19.2
<br>jupyter_client          8.6.3
<br>jupyter_core            5.8.1
<br>matplotlib-inline       0.1.7
<br>nest-asyncio            1.6.0
<br>numpy                   2.2.5
<br>odfpy                   1.4.1
<br>openpyxl                3.1.5
<br>packaging               25.0
<br>pandas                  2.2.3
<br>parso                   0.8.4
<br>pip                     25.1.1
<br>platformdirs            4.3.8
<br>prompt_toolkit          3.0.51
<br>psutil                  7.0.0
<br>pure_eval               0.2.3
<br>Pygments                2.19.
2<br>python-dateutil         2.9.0.post0
<br>pytz                    2025.2
<br>pywin32                 311
<br>pyzmq                   27.0.0
<br>six                     1.17.0
<br>sniffio                 1.3.1
<br>stack-data              0.6.3
<br>tabula-py               2.10.0
<br>tornado                 6.5.1
<br>traitlets               5.14.3
<br>tzdata                  2025.2
<br>wcwidth                 0.2.13
<br>xlrd                    2.0.1</span>


#### Java
java 24.0.2 2025-07-15

Java(TM) SE Runtime Environment (build 24.0.2+12-54)

Java HotSpot(TM) 64-Bit Server VM (build 24.0.2+12-54, mixed mode, sharing)





# Lista de Unidades

## ISGH

- [x] HLDV
- [x] HRSC
- [X] HRN
- [x] HLDV
- [x] HGWA
- [x] HRJV
- [x] HUC
- [x] UPAs

## Universidades 

- [x] UFC
- [x] UNILAB
- [X] UFAM
- [X] UVA
- [X] UFPB
- [X] UFRA
- [X] UFSM
- [X] UNB
- [X] UFS 

## SAP

- [X] SAP (3 Contratos, Múltiplas Unidades)

## Hospitais

- [ ] DF - BRASÍLIA (4 Unidades)

- [X] *DF - BRASÍLIA - ISM* 

- [X] *DF - BRASÍLIA - GUARÁ*

- [ ] *DF - BRASÍLIA - GAMA*

- [ ] *DF - BRASÍLIA - PALANLTINA*

- [ ] CE - HIAS
- [ ] CE - HGF
- [ ] CE - HNSC/GONZAGUINHA MESSEJANA
- [ ] CE - FROTINAS/GONZAGUINHAS (4 UNIDADES)
- [ ] CE - HPM
- [X] CE - EBSERH - HUWC E MEAC
- [ ] CE - HAPVIDA (5 Unidades)
- [X] CE - SPDM - HCF
- [X] CE - EBSERH - HU UNIVASF
- [X] TO - EBSERH - HDT
- [ ] RR - SESAU (Dois lotes, múltiplos hospitais)

## Outros 

- [ ] PE - SALGUEIRO TLSA
- [ ] CE - SDHDS - RESTAURANTE POPULAR
- [ ] CE - SEDUC - BANABUIU
- [ ] CE - TIJUCA
- [ ] TO - REST. POPULAR ARAGUAÍNA


