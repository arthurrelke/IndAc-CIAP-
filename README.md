# IndAc-CIAP-
Parte da metodologia desenvolvida para produção de artigo enviado para o 8° Congresso Internacional de Arquitetura da Paisagem.

# Análise de Acessibilidade Pedestre ao BRT Cuiabá: Uma Abordagem Multicriterial

> **Autor:** Arthur Esteves
> **Área:** Data Science Urbano & Planejamento de Transportes
> **Status:** Concluído (Fase de Validação Estatística)

## 📋 Sobre o Projeto

Este estudo desenvolve um framework de análise espacial para quantificar a **acessibilidade real** de 16.684 domicílios a três estações do BRT em Cuiabá-MT. Diferente de abordagens tradicionais baseadas em raios (buffers), este modelo utiliza **grafos viários** e uma **função de impedância multicriterial** que considera o esforço físico (topografia), a qualidade da infraestrutura urbana e a vulnerabilidade social dos pedestres.

---

## 📍 1. Definição da Área de Estudo

O recorte territorial situa-se na convergência das **Regiões Administrativas Norte, Oeste e Leste** de Cuiabá. A escolha desta área responde à necessidade de analisar unidades morfológicas urbanas distintas, com perfis socioeconômicos e geoambientais variados.

* **Escopo:** 19 Setores Censitários.
* **População:** 16.684 domicílios.
* **Infraestrutura:** 3 Estações BRT no eixo da Av. Historiador Rubens de Mendonça.
* **Premissa:** A acessibilidade a oportunidades (trabalho/lazer) depende das condições estruturais do caminho entre origem e destino, tratando a paisagem como um sistema urbanístico-ambiental.

*Figura 1: Situação de recorte da área de estudo.*

---

## ⚙️ 2. Metodologia: Construção do Índice de Acessibilidade (ÍndAc)

O cálculo do Índice de Acessibilidade (ÍndAc) opera através de um algoritmo em Python que integra cinco dimensões fundamentais, ponderando fatores físicos e sociais sob a ótica da **Desvantagem Acumulada** (*Cumulative Disadvantage*).

### 2.1 Modelagem de Rede (Graph Theory)

Utilizando a biblioteca `NetworkX`, o sistema viário foi convertido em um **Grafo Direcionado (MultiDiGraph)**.

* **Nós:** Interseções viárias.
* **Arestas:** Segmentos de via.
* **Diferencial:** Substituição da distância euclidiana pela distância real de rede (Levinson; El-Geneidy, 2009).

### 2.2 Função de Impedância Multicriterial

Para mensurar a "desutilidade" do deslocamento (Geurs & Van Wee, 2004), desenvolvemos uma função de custo composta por 5 dimensões:

#### A. Topografia e Esforço Físico

* **Fonte:** Modelo Digital de Terreno (MDT) via ALOS PALSAR (12.5m de resolução).
* **Processamento:** Cálculo de declividade via GDAL/QGIS.
* **Função de Custo:** Aplicação da **Tobler’s Hiking Function** (1993). Pedestres evitam rotas íngremes mesmo que mais curtas.

#### B. Hierarquia Viária (Walking Quality)

* **Base Legal:** Lei Complementar nº 232/2011.
* **Adaptação:** Baseado em Jiang et al. (2012), utiliza a hierarquia funcional como proxy para atributos latentes (largura da caixa viária, fluxo, prioridade de manutenção).
* **Observação:** Vias locais apresentaram menor "agradabilidade" (*walking quality*) que eixos estruturais no contexto local.

#### C. Infraestrutura Urbanística (IBGE 2022)

Para mitigar a falácia ecológica, utilizamos dados de **face de quadra** do Censo.

* **Variáveis (8):** Arborização, Iluminação, Meio-fio, Pavimentação, Calçadas, Rampas, Obstáculos e Esgoto.
* **Ponderação:** Média geométrica baseada em Alfonzo (2005), onde riscos à integridade física penalizam mais que desconforto térmico.

#### D. Contexto Socioterritorial

* **Densidade Demográfica:** Áreas de baixa densidade recebem penalização (até 1,2x) devido à monotonia e falta de vigilância natural (Olhos da Rua).
* **Renda do Setor:** Penalização leve (até 1,1x) em setores de baixa renda para capturar a precariedade na manutenção de equipamentos urbanos (Vasconcellos, 2001).

### 2.3 Ponderação do Índice (Justiça Socioterritorial)

O algoritmo final calcula o ÍndAc atribuindo pesos distintos para dimensões Físicas e Sociais, reconhecendo que grupos vulneráveis enfrentam barreiras maiores.

| Dimensão | Componente | Peso | Justificativa Teórica |
| --- | --- | --- | --- |
| **FÍSICA (55%)** | Distância Real | 30% | Geurs & Van Wee (2004) |
|  | Impedância Multicriterial | 25% | Parkin et al. (2007) - Qualificador qualitativo |
| **SOCIAL (45%)** | Vulnerabilidade Social | 20% | Pereira et al. (2016) |
|  | Dependência Demográfica | 15% | Capacidade de mobilidade reduzida |
|  | Renda | 10% | Desvantagem acumulada |

---

## 📊 3. Resultados e Discussão

A modelagem revelou uma dissociação crítica entre a infraestrutura do BRT e a realidade do território.

### 🚩 Insights Críticos de Infraestrutura

* **Rede vs. Linha Reta:** As distâncias reais de caminhada são, em média, **47% maiores** que a distância euclidiana.
* **Hostilidade Urbana:**
* **94%** das faces de quadra **não possuem rampas**.
* **92%** possuem **obstáculos** nas calçadas.
* **97,9%** carecem de **arborização** (crítico no clima de Cuiabá).



### 📉 O Custo do Deslocamento

* **Fator de Esforço:** A impedância calculada indica que o custo percebido é **3x superior** à distância física.
> *"Para cada 10 metros caminhados, o esforço percebido equivale a 30 metros."*


* **Tempo de Viagem:**
* Média bruta: 50,9 min.
* **Média Ajustada (Vulnerabilidade): 60,2 min** (+20% de esforço para grupos vulneráveis).



### 🗺️ Distribuição Espacial da Acessibilidade

A classificação do ÍndAc demonstrou profunda desigualdade:

* ✅ **Ideal/Aceitável (<800m):** Apenas **7%** dos domicílios.
* ⚠️ **Moderado/Ruim (>800m):** 50,6% dos domicílios.
* 🚨 **Péssimo (>1.800m):** **42,5%** dos domicílios.

**Gap de Inequidade:** Domicílios em setores de alta vulnerabilidade possuem acessibilidade **45% inferior** aos de baixa vulnerabilidade.

### 📈 Validação Estatística

* **Regressão Linear Múltipla:** . O modelo explica 96% da variação do acesso, com a distância real e a impedância sendo determinantes.
* **Modelo Logístico:** AUC = 0.990. Alta precisão na classificação de domicílios em situação crítica.

---

## 📂 Estrutura do Repositório

```text
.
├── data/
│   ├── raw/                  # Dados brutos (ALOS PALSAR, IBGE, SHP Prefeitura)
│   └── processed/            # Grafos NetworkX e GeoPackages processados
├── docs/
│   └── images/               # Figuras (Mapas de Declividade, Gráficos)
├── scripts/
│   ├── 01_graph_build.py     # Construção do Grafo e Topologia
│   ├── 02_impedance_calc.py  # Cálculo das 5 dimensões de impedância
│   └── 03_indac_model.py     # Algoritmo do Índice de Acessibilidade
└── requirements.txt          # Dependências (GeoPandas, NetworkX, Rasterio)

```
Arthur Esteves
---

*Dados baseados no Censo IBGE 2022, Prefeitura de Cuiabá e ALOS PALSAR (JAXA).*
