
#  Análise Descritiva de Acidentes em Rodovias Federais Brasileiras

Trabalho prático de **Aprendizado Descritivo** aplicado à base de dados de acidentes da Polícia Rodoviária Federal (PRF), com foco na descoberta de subgrupos relevantes via a biblioteca `pysubgroup`.

---

## Estrutura do Repositório

```
tp_ad/
├── datatran2007.csv              # Base de dados de acidentes — ano 2007
├── datatran2025.csv              # Base de dados de acidentes — ano 2025
├── subgrupos_comparativo.csv     # Resultado comparativo dos subgrupos descobertos
├── dicionario_de_dados.pdf       # Dicionário oficial de variáveis da PRF
├── projeto AD.pdf                # Enunciado/descrição do projeto
├── main.ipynb                    # Notebook principal com análises
├── links.txt                     # Fontes e referências externas
└── Readme.md                     # Este arquivo
```

---

##  Bases de Dados

### Fonte

Os dados são provenientes do portal de **Dados Abertos da PRF** ([dados.prf.gov.br](https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-da-prf)), que disponibiliza registros de todos os acidentes ocorridos em rodovias federais brasileiras, com detalhamento por ocorrência, envolvidos e veículos.

---

### `datatran2007.csv` — Acidentes em 2007

Registro histórico de acidentes em rodovias federais referente ao ano de **2007**. Serve como base de comparação temporal para identificar mudanças nos padrões de acidentes ao longo de quase duas décadas.

**Principais variáveis:**

| Variável | Descrição |
|---|---|
| `id` | Identificador único do acidente |
| `data_inversa` | Data do acidente (formato YYYY-MM-DD) |
| `dia_semana` | Dia da semana em que ocorreu o acidente |
| `horario` | Hora do acidente |
| `uf` | Unidade Federativa (estado) |
| `br` | Número da rodovia federal |
| `km` | Quilômetro da rodovia onde ocorreu o acidente |
| `municipio` | Município do acidente |
| `causa_acidente` | Causa principal do acidente |
| `tipo_acidente` | Tipo do acidente (colisão frontal, capotamento, etc.) |
| `classificacao_acidente` | Classificação quanto à gravidade (sem vítimas, com vítimas feridas, com vítimas fatais) |
| `fase_dia` | Período do dia (pleno dia, plena noite, amanhecer, anoitecer) |
| `sentido_via` | Sentido de tráfego no momento do acidente |
| `condicao_metereologica` | Condição climática no momento do acidente |
| `tipo_pista` | Tipo da pista (simples, dupla, múltipla) |
| `tracado_via` | Traçado da via (reta, curva, etc.) |
| `uso_solo` | Área urbana ou rural |
| `pessoas` | Total de pessoas envolvidas |
| `mortos` | Número de mortos |
| `feridos_graves` | Número de feridos graves |
| `feridos_leves` | Número de feridos leves |
| `ilesos` | Número de ilesos |
| `ignorados` | Envolvidos com situação ignorada |
| `feridos` | Total de feridos |
| `veiculos` | Total de veículos envolvidos |
| `latitude` / `longitude` | Coordenadas geográficas do acidente |

---

### `datatran2025.csv` — Acidentes em 2025

Versão mais recente da mesma base, referente ao ano de **2025**. Permite avaliar o cenário atual e comparar com o histórico de 2007.

**As variáveis são as mesmas do arquivo `datatran2007.csv`**, com possíveis acréscimos de colunas introduzidas pela PRF em versões mais recentes do dataset, como informações mais granulares sobre os envolvidos.

> **Nota:** Em versões mais recentes, a base pode incluir colunas de **pessoas envolvidas** com detalhes individuais como `idade`, `sexo`, `tipo_veiculo` e `estado_fisico`, que são fundamentais para a segunda pergunta de pesquisa.

---

### `subgrupos_comparativo.csv` — Resultado da Descoberta de Subgrupos

Arquivo gerado pela execução do algoritmo de descoberta de subgrupos (`pysubgroup`). Contém os subgrupos mais relevantes encontrados nas bases de 2007 e 2025, com suas métricas de qualidade para fins comparativos.

**Estrutura esperada:**

| Coluna | Descrição |
|---|---|
| `subgroup` | Descrição lógica do subgrupo (ex: `tipo_pista == 'Simples' AND condicao_metereologica == 'Chuva'`) |
| `quality` | Métrica de qualidade do subgrupo (ex: WRAcc, lift) |
| `size_sg` | Número de instâncias no subgrupo |
| `size_dataset` | Total de instâncias no dataset |
| `target_share_sg` | Proporção da variável-alvo no subgrupo |
| `target_share_dataset` | Proporção da variável-alvo na base completa |
| `ano` | Ano de referência (2007 ou 2025) |

---

## Perguntas de Pesquisa

### Pergunta 1 — Letalidade por Contexto Viário e Climático

> **Quais combinações de contexto da via (`tipo_pista` e `tracado_via`) e clima (`condicao_meteorologica`) apresentam uma taxa de letalidade — baseada na variável `mortos` — excepcionalmente superior à média nacional?**

**Objetivo:** Identificar subgrupos definidos por características físicas da rodovia e condições atmosféricas que concentram uma proporção anormalmente alta de mortes em relação ao total de acidentes. Isso permite priorizar intervenções de segurança viária nas combinações de risco mais críticas.

**Variáveis envolvidas:**
- `tipo_pista` — Pista simples, dupla ou múltipla
- `tracado_via` — Reta, curva acentuada, curva, interseção, etc.
- `condicao_metereologica` — Sol, chuva, nevoeiro, granizo, neve, etc.
- `mortos` (variável-alvo) — Número de mortos por acidente

**Abordagem analítica:** A taxa de letalidade de cada subgrupo é comparada com a média nacional. Subgrupos com desvio positivo estatisticamente significativo são considerados de risco excepcional.

---

### Pergunta 2 — Perfil de Condutores de Motocicletas em Área Rural

> **Existem perfis específicos de condutores — combinando `idade` e `sexo` — que, ao conduzir motocicletas (`tipo_veiculo`), têm um risco significativamente maior de gerar vítimas graves (`feridos_graves`) em áreas rurais (`uso_solo`)?**

**Objetivo:** Descobrir subgrupos de condutores de motocicleta, definidos por faixa etária e sexo, que estão associados a uma incidência desproporcional de feridos graves quando os acidentes ocorrem fora do perímetro urbano. Esses perfis podem embasar campanhas educativas e políticas públicas direcionadas.

**Variáveis envolvidas:**
- `idade` — Idade do condutor
- `sexo` — Sexo do condutor
- `tipo_veiculo` — Filtro aplicado: motocicleta
- `uso_solo` — Filtro aplicado: Rural
- `feridos_graves` (variável-alvo) — Número de feridos graves por acidente

**Abordagem analítica:** O dataset é filtrado para acidentes com motocicletas em área rural, e o algoritmo de descoberta de subgrupos busca combinações de `idade` e `sexo` com concentração elevada de `feridos_graves` em relação à média desse subconjunto.

---

## Instalação e Requisitos

**Python recomendado:** 3.12
> Python 3.14 apresenta incompatibilidade com a versão do `numpy` exigida pelo `pysubgroup`.

### Dependências

```bash
pip install pandas scipy pysubgroup
```

| Biblioteca | Uso |
|---|---|
| `pandas` | Leitura, limpeza e manipulação dos CSVs |
| `scipy` | Testes estatísticos e métricas de qualidade |
| `pysubgroup` | Algoritmo de descoberta de subgrupos |

---

## Como Executar

1. Clone o repositório:
   ```bash
   git clone https://github.com/lauragodinho16/tp_ad.git
   cd tp_ad
   ```

2. Instale as dependências:
   ```bash
   pip install pandas scipy pysubgroup
   ```

3. Abra o notebook principal:
   ```bash
   jupyter notebook main.ipynb
   ```

4. Execute as células sequencialmente. Os resultados dos subgrupos são exportados para `subgrupos_comparativo.csv`.

---

## Referências

- [Portal de Dados Abertos da PRF](https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dados-abertos-da-prf)
- [Documentação do pysubgroup](https://github.com/flemmerich/pysubgroup)
- [Dicionário de dados oficial](https://www.gov.br/prf/pt-br/acesso-a-informacao/dados-abertos/dicionario-acidentes)

