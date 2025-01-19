# Blaze Predictor - Bot para Apostas no Double da Blaze

## 1. Introdução

Este documento descreve em detalhes o projeto **Blaze Predictor**, um sistema automatizado projetado para prever resultados no jogo "Double" da plataforma de apostas online Blaze. O sistema é construído com ênfase em **confiabilidade, precisão e escalabilidade**, características essenciais para um sistema crítico de software. O objetivo principal é desenvolver um bot para Telegram que analisa dados em tempo real, identifica padrões e envia sinais de alta confiança para apostas, com foco especial na previsão do resultado **branco**, que oferece um pagamento de 14x o valor da aposta.

O Blaze Predictor emprega uma combinação de técnicas avançadas para alcançar a máxima precisão possível:

*   **Web Scraping:** Extração de dados em tempo real do site *Tip Miner*, que fornece o histórico de resultados do jogo "Double" da Blaze.
*   **Numerologia:** Análise dos números das casas e sua relação com os minutos das rodadas, utilizando um conjunto de regras específicas para projetar os minutos mais prováveis para a ocorrência do branco.
*   **"Score de Intensidade":** Um algoritmo que quantifica a força de um sinal de aposta com base em múltiplos fatores, atribuindo uma pontuação que reflete a probabilidade de ocorrência do branco em um determinado minuto.
*   **Machine Learning (LSTM):** Implementação futura de um modelo de rede neural recorrente (Long Short-Term Memory) para aprender padrões complexos nos dados históricos e aprimorar as previsões das cores.

Este documento descreve em detalhes a arquitetura do sistema, as regras da numerologia, o cálculo do "score de intensidade", as dependências, a configuração do ambiente, a execução do projeto, os testes, a estratégia de Machine Learning (futura), as considerações de segurança, a manutenção e a evolução do projeto, além de um apêndice com glossário, tabela de pesos do "score de intensidade" e exemplos detalhados.

### 1.1 Descrição do Projeto

O Blaze Predictor é um sistema automatizado que visa fornecer aos usuários uma vantagem estratégica no jogo "Double" da Blaze. O sistema foi projetado para operar de forma autônoma, coletando dados em tempo real, processando-os através de um conjunto de regras e algoritmos, e gerando sinais de alta probabilidade para apostas no branco. A escolha do branco como foco principal deve-se ao seu alto retorno financeiro (14x o valor da aposta).

O sistema é composto por um script de web scraping (`scraper.py`), um módulo de análise de dados (`analysis.py`), um módulo de testes unitários (`test_analysis.py`) e, futuramente, um bot do Telegram (`telegram_bot.py`). O `scraper.py` coleta dados do site *Tip Miner* a cada 30 segundos e os armazena em um banco de dados SQLite. O `analysis.py` processa esses dados, aplica as regras da numerologia, calcula o "score de intensidade" e identifica potenciais oportunidades de aposta. O `test_analysis.py` garante a integridade e a precisão das funções implementadas em `analysis.py`. O `telegram_bot.py` (a ser desenvolvido) será a interface do usuário, enviando sinais de aposta e permitindo a interação com o sistema.

### 1.2 Objetivo do MVP

O objetivo do Minimum Viable Product (MVP) é criar um bot funcional no Telegram que demonstre a viabilidade do conceito central do Blaze Predictor. O MVP deve:

1.  **Coletar Dados em Tempo Real:** Extrair continuamente os resultados das rodadas (cor, número e minuto) do jogo "Double" do site *Tip Miner*, com um intervalo de 30 segundos entre cada coleta.
2.  **Armazenar os Dados:** Salvar os dados coletados de forma estruturada e persistente em um banco de dados SQLite local.
3.  **Calcular a Numerologia:** Aplicar as regras da numerologia, com precisão, para projetar os minutos em que o branco é mais provável de ocorrer, baseado nos números das casas que saíram como "puxadores".
4.  **Calcular o "Score de Intensidade":** Avaliar a força do sinal de branco para cada minuto, considerando os fatores definidos e seus respectivos pesos.
5.  **Previsão de Vermelho/Preto (Inicial):** Implementar um modelo LSTM básico para prever a probabilidade das cores vermelha e preta no próximo minuto, servindo como uma funcionalidade secundária e para auxiliar na identificação de padrões.
6.  **Enviar Sinais via Telegram:** Enviar alertas (sinais) para o usuário quando o "score de intensidade" do branco atingir um limiar predefinido, indicando alta probabilidade de saída do branco. O sinal deve conter o minuto projetado e o "score de intensidade".
7.  **Interface Intuitiva no Bot:** Apresentar de forma clara no Telegram os resultados das últimas rodadas, com suas respectivas cores, e o "score de intensidade" para o branco, facilitando a visualização e a tomada de decisão pelo usuário.
8.  **Backtesting:** Possibilitar a realização de testes com dados históricos para avaliar a performance do bot e a eficácia das estratégias de apostas.

### 1.3 Funcionalidades Principais

O Blaze Predictor, em sua versão MVP, terá as seguintes funcionalidades principais:

*   **Coleta Automática de Dados:** O sistema coleta dados do jogo "Double" da Blaze em tempo real, a partir do site *Tip Miner*, com alta frequência (a cada 30 segundos).
*   **Armazenamento Persistente de Dados:** Os dados coletados são armazenados de forma segura e organizada em um banco de dados SQLite, permitindo a análise histórica e o treinamento do modelo de Machine Learning.
*   **Cálculo Preciso da Numerologia:** O sistema aplica as regras da numerologia de forma precisa para identificar os minutos com maior probabilidade de ocorrência do branco.
*   **Avaliação da Força do Sinal ("Score de Intensidade"):** O "score de intensidade" fornece uma medida quantitativa da confiança em cada previsão, permitindo que o usuário tome decisões mais informadas.
*   **Previsão Básica de Vermelho/Preto:** Um modelo LSTM simples auxilia na identificação de padrões e fornece previsões para as cores vermelha e preta.
*   **Notificações em Tempo Real:** O bot do Telegram envia sinais instantâneos para o usuário quando uma oportunidade de aposta no branco é identificada.
*   **Interface Amigável:** O bot oferece uma interface simples e intuitiva para o usuário visualizar os resultados das rodadas e o "score de intensidade".
*   **Backtesting com Dados Históricos:** O usuário pode simular a operação do bot em períodos passados para avaliar a performance e ajustar parâmetros.

## 2. Regras da Numerologia

A numerologia é a base para a identificação de potenciais oportunidades de apostas no branco no jogo "Double" da Blaze. Ela se baseia na relação entre os números das casas ("puxadores") que saem antes e depois de um branco e o minuto em que o branco ocorre.

### 2.1 Conceitos Chave

*   **Branco:** O resultado alvo das nossas previsões. Representado pelo número 0 na roleta e paga 14x o valor da aposta. É a cor menos frequente, mas com maior potencial de lucro.
*   **Puxadores:** São as duas casas que saíram imediatamente antes e as duas casas que saíram imediatamente depois de um branco. São fundamentais para o cálculo da numerologia.
*   **Minuto do Branco:** O minuto exato em que o branco ocorreu (ex: "07:03"). Cada minuto possui duas rodadas, como indicado no *Tip Miner*.
*   **Minuto Projetado:** O minuto calculado com base na soma do número do puxador com o minuto em que ele saiu.
*   **Taxa de Erro:** Uma tolerância de +/- 1 minuto em relação ao minuto projetado. Isso é considerado devido a variações e atrasos na atualização dos dados da plataforma.
*   **Convergência:** Quando dois ou mais puxadores, através de seus cálculos, apontam para o mesmo minuto projetado (dentro da taxa de erro), isso é considerado um sinal forte e aumenta a probabilidade de um branco naquele minuto.
*   **Minutos Águia:** Os números 4 e 11 são considerados "minutos águia" e têm um significado especial na análise. Sua presença como puxadores.
*   **"Espelho":** A ocorrência de um branco em um minuto que é simétrico a um branco anterior (ex: branco em 00:59 e outro em 01:08, com diferença de 9 minutos, que é um número primo). Esse padrão sugere uma possível repetição e deve ser considerado na análise.
*   **"Hora Boa"/"Hora Ruim":** Períodos do dia com maior ou menor frequência de brancos. A "hora boa" geralmente apresenta uma alta taxa de acerto dos minutos projetados. Esses períodos são identificados pela análise da frequência de brancos em cada hora do dia.
*   **"Recuperação da Casa":** Um período prolongado (mais de 15 minutos) sem a ocorrência de brancos. Esse padrão indica que a plataforma pode estar ajustando os padrões para recuperar as perdas, e deve ser tratado com cautela.
*   **"Mini-Chuva" de Brancos:** Ocorrência de 4 ou mais brancos em um curto intervalo de tempo (5 ou 10 minutos). Esse padrão indica uma alta probabilidade de ocorrência de novos brancos em um futuro próximo.

### 2.2 Regras de Cálculo

1.  **Identificação dos Puxadores:** Para cada branco, identificamos os quatro puxadores: as duas casas anteriores e as duas posteriores, anotando seus números, cores e minutos.
2.  **Cálculo do Minuto Projetado:** Para cada puxador, somamos o número da casa com o minuto em que ele saiu.
    *   **Exemplo:** Se um puxador vermelho com número 7 saiu em `10:23`, o minuto projetado é `10:23 + 7 = 10:30`.
    *   **Observação:** O número 10 é somado como 10, e o número 0 (branco) é desconsiderado, tendo tratamento especial e contando apenas com 2 puxadores.
3.  **Aplicação da Taxa de Erro:** Consideramos uma taxa de erro de +/- 1 minuto para cada minuto projetado.
    *   **Exemplo:** Para o minuto projetado `10:30`, consideramos também `10:29` e `10:31`.
4.  **Convergência:** Se dois ou mais puxadores, através de seus cálculos, apontarem para o mesmo minuto (dentro da taxa de erro), isso é considerado um sinal de **convergência** e aumenta a probabilidade de um branco naquele minuto.
  essa é a numerologia da blaze: primeiro o numero da casa depois a cor depois o que o numero da casa significa
0	Branco	Não se aplica	
1	Vermelho	Minuto da casa + 1	
2	Vermelho	Minuto da casa + 2	
3	Vermelho	Minuto da casa + 3	
4	Vermelho	Minuto da casa + 4	
5	Vermelho	Minuto da casa + 5	
6	Vermelho	Minuto da casa + 6	
7	Vermelho	Minuto da casa + 7	
8	Preto	Minuto da casa + 8	
9	Preto	Minuto da casa + 9	
10	Preto	Minuto da casa + 10	
11	Preto	Minuto da casa + 2	
12	Preto	Minuto da casa + 3	Na numerologia 12 e considerado 1+2=3
13	Preto	Minuto da casa + 4	Na numerologia 13 e considerado 1+3=4
14	Preto	Minuto da casa + 5	Na numerologia 14 e considerado 1+4=5

os numeros 4 e 11 são considerados minutos "aguias" e passam a valer como "+2 na soma do calculo" essa consideração SOMENTE entra em questão no calculo do branco, quando estamos analisando as cores vermelho e preto elas tem valor normal de 4 e 2 em questão.
### 2.3 Exemplos Práticos

**Exemplo 1:**

Suponha que um branco saiu em `15:30`. Os puxadores identificados são:

*   `15:29` - `5` vermelho
*   `15:29` - `11` preto 
*   `15:31` - `2` vermelho
*   `15:31` - `9` preto

**Cálculos:**

*   `15:29 + 5 = 15:34` (taxa de erro: `15:33`,`15:34`)
*   `15:29 + 11 = 15:31` (taxa de erro: `15:30`, 15:32`)
*   `15:31 + 2 = 15:33` (taxa de erro: `15:32`, `15:34`)
*   `15:32 + 9 = 15:41` (taxa de erro: `15:40`, `15:42`)

**Análise:**

*   Há uma **convergência** para o minuto `15:33` (e também `15:32` e `15:34`, considerando a taxa de erro), pois dois puxadores diferentes apontam para ele.
*   O puxador `11` preto em `15:29` é um **minuto águia**, o que reforça a probabilidade de um branco, se estiver no intervalo de minutos especificado.

**Exemplo 2:**

Suponha que um branco saiu em `07:03`

*   `07:02` - `1` vermelho (primeira rodada)
*   `07:02` - `7` vermelho (segunda rodada)
*   `07:03` - `12` preto (segunda rodada)
*   `07:04` - `10` preto (primeira rodada)

**Cálculos:**

*   `07:02` + `1` = `07:03`
*   `07:02` + `7` = `07:09`
*   `07:03` + `3` = `07:06`
*   `07:04` + `10` = `07:14`

**Minutos Projetados (com taxa de erro):**

*   De `07:03`: `07:02`, `07:03`, `07:04`
*   De `07:09`: `07:08`, `07:09`, `07:10`
*   De `07:06`: `07:05`, `07:06`, `07:07`
*   De `07:14`: `07:13`, `07:14`, `07:15`

**Convergência:**

*   O minuto projetado **`07:03`** (pelo puxador `1` vermelho) coincide com o minuto do branco, indicando uma previsão acertada pela numerologia.

**Exemplo 3:**

Analisando o branco que saiu no minuto 01:08

*   **Puxadores:**
    *   `01:07` - `6` vermelho
    *   `01:07` - `2` vermelho
    *   `01:09` - `4` vermelho
    *   `01:10` - `8` preto

*   **Cálculos:**
    *   `01:07 + 6 = 01:11`
    *   `01:07 + 2 = 01:08`
    *   `01:09 + 4 = 01:13`
    *   `01:10 + 8 = 01:18`

*   **Minutos Projetados (com taxa de erro):**
    *   De `01:11`: `01:10`, `01:11`, `01:12`
    *   De `01:08`: `01:07`, `01:08`, `01:09`
    *   De `01:13`: `01:12`, `01:13`, `01:14`
    *   De `01:18`: `01:17`, `01:18`, `01:19`
    *   

*   **Convergência:**
    *   O minuto projetado **`01:08`** (pelo puxador `2` vermelho) coincide com o minuto do branco, indicando uma previsão acertada pela numerologia.
*   **Presença dos Minutos Águia:** O puxador `4` vermelho em `01:09` é um **minuto águia**. Embora seu cálculo não aponte diretamente para 01:08, sua presença indica que a leitura da sequencia do numero 2 com o minuto aguia pode ser uma forte evidencia de branco, e nesse caso a presença desses dois fatores corrobora para um branco exato.
*   **"Espelho" de 00:59:** O branco de 01:08 "espelha" o branco de 00:59, com uma diferença de 9 minutos, o que reforça a possibilidade do branco
| **(D) "Recuperação da Casa"**                | -8    |
| **(E) Minutos Águia (4 e 11)**                | +6    |
| **(F) Tempo desde o último branco**            | +3/+1/-2|
| **(G) Padrão do dia anterior**                | +2    |
| **(H) Soma dos puxadores > 20**                | +3    |
| **(I) Padrões de Cores**                     | +1    |
| **(J) "Espelho"**                               | +6    |
| **(K) Força Individual dos Puxadores**         | +1 a +5 |
| **(L) Combinação de Puxadores**                | +2 a +8 |
| **(M) Combinação de Puxadores com Minutos Águias** | +3 a +7 |

### 3.3 Pesos dos Fatores

Os pesos atribuídos a cada fator refletem sua importância relativa na determinação do "score de intensidade".  Esses pesos são **sugestões iniciais** baseadas na experiência e observações do comportamento do jogo "Double".  Eles serão **ajustados e otimizados** ao longo do tempo, com base em testes e na análise de dados coletados.

### 3.4 Cálculo do "Score de Intensidade" (Fórmula)

O "score de intensidade" é calculado somando-se os pontos de cada fator, de acordo com a seguinte fórmula:

**Próximos Passos:**

1.  **Copiar e colar o conteúdo do `README.md` no arquivo `README.md` do seu repositório GitHub.**
2.  **Implementar as funções auxiliares do "score de intensidade" em `analysis.py`.**

Score de Intensidade = (A * Peso_A) + (B * Peso_B) + (C * Peso_C) + ... + (M * Peso_M)


Onde:

*   **A, B, C, ..., M** representam os valores dos fatores (ex: número de cálculos convergentes, presença de "mini-chuva", etc.).
*   **Peso_A, Peso_B, Peso_C, ..., Peso_M** representam os pesos atribuídos a cada fator.

### 3.5 Exemplos de Cálculo

**Exemplo 1:**

*   Minuto em análise: "15:33"
*   Convergência de Cálculos: 2 cálculos convergem para 15:33 (+6 pontos)
*   "Mini-chuva": Ocorreu uma "mini-chuva" nos últimos 5 minutos (+7 pontos)
*   Frequência de Brancos na Hora: 6 brancos na hora 15:00-15:59 (+3 pontos)
*   "Recuperação da Casa": Não se aplica (houve brancos recentes) (0 pontos)
*   Minutos Águia: Um puxador é um minuto águia, e se encaixa no intervalo (+6 pontos)
*   Tempo desde o último branco: Último branco foi há 8 minutos (+1 ponto)
*   Padrão do dia anterior: Similar ao dia anterior (+2 pontos)
*   Soma dos puxadores: 18 (0 pontos)
*   Padrões de Cores: Sequência de 4 vermelhos (+1 ponto)
*   "Espelho": Não se aplica (0 pontos)

**Score de Intensidade:** 6 + 7 + 3 + 0 + 6 + 1 + 2 + 0 + 1 + 0 = **26**

**Exemplo 2:**

*   Minuto em análise: "22:15"
*   Convergência de Cálculos: Nenhum cálculo converge para 22:15 (0 pontos)
*   "Mini-chuva": Não ocorreu nos últimos 10 minutos (0 pontos)
*   Frequência de Brancos na Hora: 3 brancos na hora 22:00-22:59 (+2 pontos)
*   "Recuperação da Casa": Não se aplica (0 pontos)
*   Minutos Águia: Nenhum puxador é minuto águia (0 pontos)
*   Tempo desde o último branco: Último branco foi há 2 minutos (-2 pontos)
*   Padrão do dia anterior: Diferente do dia anterior (0 pontos)
*   Soma dos puxadores: 25 (+3 pontos)
*   Padrões de Cores: Sequência de 2 pretos (0 pontos)
*   "Espelho": Não se aplica (0 pontos)

**Score de Intensidade:** 0 + 0 + 2 + 0 + 0 - 2 + 0 + 3 + 0 + 0 = **3**

**Exemplo 3:**

*   Minuto em análise: "00:59"
*   `00:55` - `4` vermelho (águia)
*   `00:58` - `9` preto
*   `01:00` - `6` preto
*   `01:01` - `8` preto

**Cálculos:**

*   `00:55 + 4 = 00:59`
*   `00:58 + 9 = 01:07`
*   `01:00 + 6 = 01:06`
*   `01:01 + 8 = 01:09`

**Minutos Projetados (com taxa de erro):**

*   De `00:59`: `00:58`, `00:59`, `01:00`
*   De `01:07`: `01:06`, `01:07`, `01:08`
*   De `01:06`: `01:05`, `01:06`, `01:07`
*   De `01:09`: `01:08`, `01:09`, `01:10`

*   **Convergência:** O minuto `01:06`, `01:07`, `01:08` são sugeridos, mas não o minuto em que saiu o branco.
*   **Score de Intensidade:**
    *   Convergência: + 6 (para os três minutos)
    *   "Mini-chuva": +7 (brancos em 00:59, 01:08 e 01:18)
    *   Frequência na Hora: +5 (mais de 8 brancos)
    *   "Recuperação da Casa": 0
    *   "Minuto Águia": +6 (número 4 como puxador e entre os minutos `58` e `08`)
    *   Tempo desde o último branco: +1 (o último branco foi a 9 minutos, intervalo de tempo ideal.)
    *   Padrão do dia anterior: +2 (considerando apenas como base)
    *   Soma dos puxadores > 20: 0
    *   **Total: +27**

**Observações:**

*   O "score de intensidade" é uma ferramenta **dinâmica** e será constantemente **reavaliado e ajustado** com base em novos dados e observações.
*   O objetivo é que o "score de intensidade" seja um **indicador confiável** da probabilidade de ocorrência do branco, permitindo que o bot envie sinais de alta precisão.

## 4. Arquitetura do Sistema

O Blaze Predictor é composto por quatro módulos principais: `scraper.py`, `analysis.py`, `test_analysis.py`, e `telegram_bot.py` (a ser desenvolvido). Cada módulo tem uma responsabilidade específica e interage com os outros de forma modular e bem definida.

### 4.1 Diagrama de Blocos

+-----------------+       +-----------------+       +-----------------+
|   scraper.py    | ----> |  blaze_predictor.db | <---- | analysis.py     |
+-----------------+       +-----------------+       +-----------------+
^                                                     |
|                                                     v
|                                            +-----------------+
|                                            | test_analysis.py|
|                                            +-----------------+
|
| (Futuro)
v
+-----------------+
| telegram_bot.py |
+-----------------+


### 4.2 Descrição dos Módulos

#### 4.2.1 `scraper.py`

*   **Responsabilidade:** Coletar dados em tempo real do jogo "Double" da Blaze, através do site *Tip Miner*.
*   **Funcionalidades:**
    *   Realizar requisições HTTP para o *Tip Miner*.
    *   Analisar o HTML da página e extrair as informações relevantes das rodadas (cor, número e minuto).
    *   Armazenar os dados coletados no banco de dados SQLite (`blaze_predictor.db`).
    *   Chamar as funções do módulo `analysis.py` para calcular a numerologia e o "score de intensidade" para cada nova rodada.
    *   Agendar a execução da coleta de dados a cada 30 segundos.
*   **Tecnologias:** `requests`, `beautifulsoup4`, `sqlite3`, `schedule`, `python-dotenv`.

#### 4.2.2 `analysis.py`

*   **Responsabilidade:**  Processar os dados coletados, aplicar as regras da numerologia, calcular o "score de intensidade" e, futuramente, realizar previsões usando o modelo LSTM.
*   **Funcionalidades:**
    *   `identificar_puxadores(minuto_branco)`:  Identifica os quatro puxadores (duas rodadas antes e duas depois) de um determinado minuto em que o branco ocorreu.
    *   `calcular_minutos_projetados(puxadores)`: Calcula os minutos projetados para a ocorrência do branco com base na numerologia dos puxadores.
    *   `calcular_score_intensidade(minuto)`: Calcula o "score de intensidade" para um determinado minuto, considerando diversos fatores.
    *   Funções auxiliares para o cálculo do "score de intensidade":
        *   `_verificar_mini_chuva(minuto, conn, cursor)`: Verifica a ocorrência de "mini-chuvas" de brancos.
        *   `_verificar_recuperacao_casa(minuto, conn, cursor)`: Verifica se a casa está em período de recuperação.
        *   `_calcular_frequencia_brancos(minuto, conn, cursor)`: Calcula a frequência de brancos na hora atual.
        *   `_verificar_minuto_aguia(puxadores)`: Verifica a presença de minutos águia (4 e 11) entre os puxadores.
        *   `_verificar_padrao_dia_anterior(minuto, conn, cursor)`: Verifica a similaridade com o padrão do dia anterior.
        *   `_verificar_espelho(minuto, conn, cursor)`: Verifica a ocorrência de um padrão de "espelho".
        *   `_verificar_soma_puxadores(puxadores)`: Verifica se a soma dos números dos puxadores é maior que 20.
*   **Tecnologias:** `sqlite3`, `python-dotenv`, `datetime`.

#### 4.2.3 `test_analysis.py`

*   **Responsabilidade:**  Testar as funções do módulo `analysis.py` para garantir que elas estão funcionando corretamente e produzindo os resultados esperados.
*   **Funcionalidades:**
    *   Testes unitários para `calcular_minutos_projetados`, `identificar_puxadores` e `calcular_score_intensidade`, e para as funções auxiliares.
    *   Configuração de um banco de dados temporário para os testes.
*   **Tecnologias:** `unittest`, `sqlite3`, `dotenv`.

#### 4.2.4 `telegram_bot.py` (Futuro)

*   **Responsabilidade:** Fornecer uma interface para o usuário interagir com o sistema e receber os sinais de apostas.
*   **Funcionalidades:**
    *   Enviar notificações (sinais) de alta probabilidade para apostas no branco, com base no "score de intensidade".
    *   Permitir que o usuário consulte o "score de intensidade" para um determinado minuto.
    *   Exibir as últimas rodadas coletadas.
    *   Fornecer estatísticas sobre o desempenho do sistema.
    *   Implementar comandos para iniciar/parar o sistema.
*   **Tecnologias:** `python-telegram-bot`, `sqlite3`.

### 4.3 Fluxo de Dados

1.  O `scraper.py` acessa o site *Tip Miner* a cada 30 segundos e extrai os dados das novas rodadas (cor, número e minuto).
2.  Os dados coletados são armazenados no banco de dados SQLite (`blaze_predictor.db`).
3.  O `scraper.py` chama as funções do `analysis.py` para:
    *   Identificar os puxadores para o último minuto coletado.
    *   Calcular os minutos projetados com base na numerologia.
    *   Calcular o "score de intensidade" para o último minuto coletado.
4.  O `scraper.py` atualiza o banco de dados com o "score de intensidade" calculado.
5.  (Futuro) O `telegram_bot.py` consulta o banco de dados, verifica o "score de intensidade" e envia sinais de alta probabilidade para o usuário.

3.  **Escrever os testes unitários para essas funções em `test_analysis.py`.**
*   
*   *   **4.3.1. Coleta de Dados (`scraper.py`)**: O `scraper.py` é responsável por coletar dados do site Tip Miner. Ele faz isso a cada 30 segundos e armazena os dados no banco de dados.
*   **4.3.2. Análise de Dados (`analysis.py`)**: O `analysis.py` processa os dados coletados, aplica as regras da numerologia e calcula o "score de intensidade". Ele atualiza o banco de dados com esses valores.
*   **4.3.3. Testes (`test_analysis.py`)**: O `test_analysis.py` contém testes unitários para as funções do `analysis.py`, garantindo que a numerologia e o cálculo do "score de intensidade" estejam corretos.
*   **4.3.4. Bot do Telegram (`telegram_bot.py`)**: O `telegram_bot.py` (a ser desenvolvido) enviará sinais de alta probabilidade para apostas no branco com base no "score de intensidade".

### 4.4 Banco de Dados

O sistema utiliza um banco de dados SQLite (`blaze_predictor.db`) para armazenar os dados coletados e os resultados das análises. A estrutura da tabela `historico` é a seguinte:

| Campo             | Tipo      | Descrição                                                                                                                                |
| :---------------- | :-------- | :--------------------------------------------------------------------------------------------------------------------------------------- |
| `id`              | INTEGER   | Identificador único para cada rodada (chave primária, autoincremento).                                                                    |
| `timestamp`       | TEXT      | Data e hora da coleta dos dados (formato ISO 8601).                                                                                      |
| `minuto`         | TEXT      | Minuto da rodada (formato HH:MM).                                                                                                      |
| `cor`             | TEXT      | Cor da casa (vermelho, preto ou branco).                                                                                                 |
| `numero`          | INTEGER   | Número da casa.                                                                                                                           |
| `score_intensidade` | REAL      | "Score de intensidade" calculado para o minuto, refletindo a probabilidade de ocorrência de um branco (valor entre 0 e 100, aproximadamente). |

## 5. Dependências

O projeto utiliza as seguintes bibliotecas Python:

*   **`requests`:** Para fazer requisições HTTP ao site *Tip Miner*.
*   **`beautifulsoup4`:** Para analisar o HTML e extrair os dados das rodadas.
*   **`sqlite3`:**  Para interagir com o banco de dados SQLite.
*   **`schedule`:** Para agendar a execução do script de coleta de dados.
*   **`python-telegram-bot`:** Para criar e interagir com o bot do Telegram (será adicionada posteriormente).
*   **`python-dotenv`:** Para gerenciar variáveis de ambiente.
*   **`datetime`:** Para trabalhar com datas e horas.
*   **`pandas`:** Para análise de dados (pode ser adicionada posteriormente, se necessário).
*   **`numpy`:** Para operações numéricas (pode ser adicionada posteriormente, se necessário).
*   **`tensorflow`/`keras`:** Para o modelo de Machine Learning (será adicionada posteriormente).
*   **`unittest`:** Para testes unitários.

### 5.1 Lista de Bibliotecas Python

| Biblioteca           | Versão (Exemplo) | Descrição                                                                                                       |
| :------------------- | :--------------- | :-------------------------------------------------------------------------------------------------------------- |
| `requests`           | 2.31.0          | Realiza requisições HTTP.                                                                                       |
| `beautifulsoup4`      | 4.12.2          | Analisa HTML e extrai dados.                                                                                    |
| `sqlite3`            | 3.36.0          | Interage com o banco de dados SQLite.                                                                           |
| `schedule`           | 1.2.1           | Agenda a execução de tarefas.                                                                                   |
| `python-telegram-bot` | 20.7            | Cria e interage com o bot do Telegram (será adicionada posteriormente).                                         |
| `python-dotenv`        | 1.0.0           | Gerencia variáveis de ambiente.                                                                                 |
| `datetime`           | (built-in)       | Manipulação de datas e horas.                                                                                   |
| `pandas`             | 2.1.4           | Análise de dados (opcional).                                                                                       |
| `numpy`              | 1.26.3          | Operações numéricas (opcional).                                                                                  |
| `tensorflow`         | 2.15.0          | Framework para Machine Learning (será adicionada posteriormente).                                                |
| `keras`              | 2.15.0         | API de alto nível para TensorFlow (será adicionada posteriormente).                                             |
| `unittest`           | (built-in)       | Framework para testes unitários.                                                                                |

### 5.2 Instruções de Instalação

Todas as dependências podem ser instaladas via `pip` usando o arquivo `requirements.txt` (que deve ser criado na raiz do projeto).

**Passos:**

1.  **Crie o arquivo `requirements.txt`** na raiz do projeto e adicione as seguintes linhas, incluindo as bibliotecas e suas versões, se necessario:

    ```
    requests==2.31.0
    beautifulsoup4==4.12.2
    python-dotenv==1.0.0
    schedule==1.2.1
    ```

    *   **Observação:** Adicione ou remova as bibliotecas conforme necessário, de acordo com o desenvolvimento do projeto.

2.  **Instale as dependências:**
    *   Abra o prompt de comando ou terminal.
    *   Navegue até o diretório raiz do projeto (`blaze-predictor1`).
    *   Ative o ambiente virtual:
        *   No Windows:  `venv\Scripts\activate`
        *   No Linux/macOS: `source venv/bin/activate`
    *   Execute o comando:

        ```bash
        pip install -r requirements.txt
        ```

## 6. Configuração do Ambiente

Para executar o projeto, é necessário configurar o ambiente de desenvolvimento e algumas variáveis de ambiente.

### 6.1 Criação do Ambiente Virtual

É altamente recomendável usar um ambiente virtual para isolar as dependências do projeto. Para criar um ambiente virtual, siga os passos abaixo:

1.  Abra o prompt de comando ou terminal.
2.  Navegue até o diretório onde você clonou o repositório (`blaze-predictor1`):

    ```bash
    cd /caminho/para/blaze-predictor1
    ```

3.  Crie o ambiente virtual:

    ```bash
    python3 -m venv venv
    ```

    (Se `python3` não funcionar, tente `python`)

4.  Ative o ambiente virtual:

    *   No Windows:

        ```bash
        venv\Scripts\activate
        ```

    *   No Linux/macOS:

        ```bash
        source venv/bin/activate
        ```

### 6.2 Instalação de Dependências

Com o ambiente virtual ativado, instale as dependências listadas na seção 5.2 usando o `pip`:

```bash
pip install -r requirements.txt
6.3 Configuração do Arquivo .env
O arquivo .env é usado para armazenar variáveis de ambiente que não devem ser incluídas diretamente no código.

Criação do Arquivo .env:

Na raiz do projeto (blaze-predictor1), crie um arquivo de texto chamado .env (sem nenhuma extensão, apenas .env).
Conteúdo do Arquivo .env:

Adicione a seguinte linha ao arquivo .env:

DATABASE_PATH=./blaze_predictor.db
Essa linha define o caminho para o banco de dados SQLite que será usado pelo projeto. O ponto (.) indica que o banco de dados será criado no mesmo diretório onde está o script scraper.py.

Importante:  O arquivo .env deve ser adicionado ao .gitignore para evitar que informações sensíveis sejam commitadas no repositório.

6.4 Configuração do Banco de Dados
O projeto utiliza um banco de dados SQLite para armazenar os dados coletados e os resultados das análises.  O banco de dados será criado automaticamente pelo script scraper.py na primeira vez que ele for executado, no local especificado pela variável DATABASE_PATH no arquivo .env.

Estrutura da Tabela historico:

Campo	Tipo	Descrição
id	INTEGER	Identificador único para cada rodada (chave primária, autoincremento).
timestamp	TEXT	Data e hora da coleta dos dados (formato ISO 8601).
minuto	TEXT	Minuto da rodada (formato HH:MM).
cor	TEXT	Cor da casa (vermelho, preto ou branco).
numero	INTEGER	Número da casa.
score_intensidade	REAL	"Score de intensidade" calculado para o minuto, refletindo a probabilidade de ocorrência de um branco (valor entre 0 e 100, aproximadamente).

Exportar para as Planilhas
Observações:

O banco de dados é criado automaticamente pelo scraper.py se não existir.
A estrutura da tabela historico é definida na função create_database() dentro do scraper.py.
7. Execução do Projeto
7.1 Executando o scraper.py
O scraper.py é o ponto de entrada principal do projeto. Ele é responsável por coletar dados, calcular a numerologia e o "score de intensidade" e armazenar os resultados no banco de dados.

Para executar o scraper.py:

Abra o prompt de comando ou terminal.

Navegue até o diretório raiz do projeto (blaze-predictor1):

Bash

cd /caminho/para/blaze-predictor1
Ative o ambiente virtual:

No Windows:

Bash

venv\Scripts\activate
No Linux/macOS:

Bash

source venv/bin/activate
Execute o script:

Bash

python scraper.py
O que acontece quando o scraper.py é executado:

O script se conecta ao banco de dados SQLite (criando-o se ele não existir).
Coleta os dados mais recentes do jogo "Double" do site Tip Miner.
Armazena os dados coletados (timestamp, minuto, cor, número) no banco de dados.
Chama as funções do analysis.py para calcular a numerologia e o "score de intensidade" para o minuto atual.
Atualiza o banco de dados com o "score de intensidade" calculado.
Aguarda 30 segundos e repete o processo.
7.2 Executando os Testes Unitários
Os testes unitários são implementados no arquivo test_analysis.py e usam a biblioteca unittest.

Para executar os testes unitários:

Abra o prompt de comando ou terminal.

Navegue até o diretório raiz do projeto (blaze-predictor1):

Bash

cd /caminho/para/blaze-predictor1
Ative o ambiente virtual:

No Windows:

Bash

venv\Scripts\activate
No Linux/macOS:

Bash

source venv/bin/activate
Execute os testes:

Bash

python -m unittest test_analysis.py
O que acontece quando os testes são executados:

O unittest carrega e executa os testes definidos na classe TestAnalysis no arquivo test_analysis.py.
Cada função de teste (test_...) é executada individualmente.
Os resultados dos testes (sucessos, falhas e erros) são exibidos no console.
7.3 Executando o Bot do Telegram (Futuro)
A implementação do bot do Telegram (telegram_bot.py) ainda está pendente.  Quando estiver concluída, esta seção descreverá como executar o bot e interagir com ele.

7.4 Monitoramento e Logs
Atualmente, o scraper.py imprime mensagens no console indicando o sucesso ou a falha na coleta e armazenamento de dados.  Para um monitoramento mais robusto, recomenda-se a implementação de um sistema de logs que registre eventos importantes, erros e exceções em um arquivo de log.

Exemplo de mensagem de log:

2023-12-29 15:30:00 - Dados coletados e armazenados com sucesso.
2023-12-29 15:30:00 - Score de intensidade (25.0) calculado e armazenado para o minuto 15:30.
2023-12-29 15:30:30 - Dados coletados e armazenados com sucesso.
2023-12-29 15:30:30 - Score de intensidade (18.0) calculado e armazenado para o minuto 15:30.
2023-12-29 15:31:00 - Dados coletados e armazenados com sucesso.
2023-12-29 15:31:00 - Score de intensidade (32.0) calculado e armazenado para o minuto 15:31.
Implementação Futura:

Utilizar a biblioteca logging do Python para registrar eventos em um arquivo de log.
Definir diferentes níveis de log (DEBUG, INFO, WARNING, ERROR, CRITICAL) para categorizar os eventos.
Incluir timestamps nos logs para facilitar a análise temporal.
8. Testes
Os testes são uma parte fundamental do projeto Blaze Predictor, garantindo a qualidade, a confiabilidade e a precisão do sistema.  Eles são implementados no arquivo test_analysis.py.

8.1 Testes Unitários
Os testes unitários verificam o funcionamento individual de cada função do módulo analysis.py.  Eles são escritos usando a biblioteca unittest do Python.

Funções Testadas:

calcular_minutos_projetados(puxadores): Verifica se os minutos projetados estão sendo calculados corretamente com base na numerologia dos puxadores.
identificar_puxadores(minuto_branco): Verifica se os puxadores corretos (duas rodadas antes e duas depois do branco) estão sendo identificados e retornados.
calcular_score_intensidade(minuto): Verifica se o "score de intensidade" está sendo calculado corretamente, considerando todos os fatores e seus pesos (esta função e suas funções auxiliares serão testadas quando forem implementadas).
Exemplo de um Teste Unitário (para calcular_minutos_projetados):

Python

def test_calcular_minutos_projetados(self):
    """Testes para a função calcular_minutos_projetados."""
    # Teste com puxadores sem números águia
    puxadores = [
        {'minuto': '10:02', 'numero': 1, 'cor': 'vermelho'},
        {'minuto': '10:02', 'numero': 7, 'cor': 'vermelho'},
        {'minuto': '10:03', 'numero': 12, 'cor': 'preto'},
        {'minuto': '10:04', 'numero': 10, 'cor': 'preto'}
    ]
    minutos_projetados = analysis.calcular_minutos_projetados(puxadores)
    self.assertCountEqual(minutos_projetados, ['10:01', '10:02', '10:03', '10:08', '10:09', '10:10', '10:15', '10:16', '10:14', '10:13', '10:14', '10:15'])
Este teste verifica se a função calcular_minutos_projetados retorna os minutos corretos, aplicando a regra da numerologia e a taxa de erro.

8.2 Testes de Integração
Os testes de integração verificam a interação entre o scraper.py e o analysis.py.  Eles garantem que os dados coletados pelo scraper.py estão sendo processados corretamente pelas funções de análise e que o "score de intensidade" está sendo armazenado no banco de dados.

Exemplo de Teste de Integração:

Executar o scraper.py por um período curto (ex: 5 minutos).
Verificar se o banco de dados está sendo populado com os dados coletados (timestamp, minuto, cor, número).
Verificar se a coluna score_intensidade está sendo preenchida com valores numéricos.
Inspecionar manualmente alguns registros no banco de dados para verificar se o "score de intensidade"
*   8.2.1. **Verificação da Coleta de Dados:**
    *   Após a execução do `scraper.py`, verificar se o banco de dados (`blaze_predictor.db`) foi criado e se a tabela `historico` contém os registros das rodadas coletadas.
    *   Utilizar um software de visualização de banco de dados SQLite (como o DB Browser for SQLite) para inspecionar os dados.
*   8.2.2. **Verificação do Cálculo do "Score de Intensidade":**
    *   Verificar se a coluna `score_intensidade` na tabela `historico` está sendo preenchida com valores numéricos.
    *   Selecionar alguns minutos e calcular manualmente o "score de intensidade" com base nos dados da tabela e nas regras definidas. Comparar os resultados manuais com os valores calculados pelo sistema para validar a lógica.

### 8.3 Testes de Ponta a Ponta

Os testes de ponta a ponta simulam o fluxo completo do sistema, desde a coleta de dados até a geração de sinais.  Eles são essenciais para validar a integração entre todos os módulos e verificar o comportamento do sistema em um cenário mais próximo do real.

*   8.3.1. **Simulação com Dados Históricos (Backtesting):**
    *   Coletar um conjunto de dados históricos do *Tip Miner* que abranja um período de tempo significativo (várias horas ou dias).
    *   Configurar o `scraper.py` para ler os dados desse histórico em vez de coletá-los em tempo real. Isso pode ser feito, por exemplo, modificando a função `get_data()` para ler de um arquivo CSV ou de uma tabela separada no banco de dados.
    *   Executar o `scraper.py`. O sistema processará os dados históricos como se fossem dados em tempo real, calculando a numerologia e o "score de intensidade".
    *   Analisar os resultados armazenados no banco de dados, focando nos minutos com alto "score de intensidade" e comparando-os com os resultados reais do branco no histórico.
    *   Avaliar a performance do sistema com base em métricas como:
        *   **Precisão:** Porcentagem de acertos (previsões corretas de branco).
        *   **Recall:**  Porcentagem de brancos previstos corretamente em relação ao total de brancos ocorridos.
        *   **F1-Score:**  Média harmônica entre precisão e recall.
        *   **Lucratividade:**  Simular apostas com base nos sinais gerados e calcular o lucro ou prejuízo obtido.
    *   Repetir os testes com diferentes conjuntos de dados históricos e ajustar os parâmetros do sistema (pesos do "score de intensidade", limiar para envio de sinais, etc.) conforme necessário.

*   8.3.2. **Testes em Tempo Real (sem apostas):**
    *   Após os testes com dados históricos, executar o `scraper.py` em tempo real, coletando dados do *Tip Miner* e calculando o "score de intensidade".
    *   Configurar o sistema para **não** enviar sinais de aposta pelo Telegram (modo de observação).
    *   Monitorar os sinais gerados e compará-los com os resultados reais do jogo "Double" na Blaze.
    *   Avaliar a performance do sistema em tempo real e identificar potenciais ajustes no algoritmo ou nos parâmetros.

### 8.4 Backtesting

O backtesting é uma etapa crucial para avaliar a eficácia do Blaze Predictor antes de colocá-lo em operação com apostas reais. Ele permite simular a operação do sistema em dados históricos e verificar sua performance em diferentes cenários.

*   **Objetivo:** Avaliar a lucratividade e a precisão do sistema em dados passados.
*   **Metodologia:**
    1.  Coletar um conjunto de dados históricos abrangente do *Tip Miner*.
    2.  Configurar o `scraper.py` para processar esses dados históricos em vez de coletar dados em tempo real.
    3.  Definir uma estratégia de apostas (ex: valor da aposta, "score de intensidade" mínimo para apostar, etc.).
    4.  Executar o `scraper.py` para simular a operação do sistema no período de tempo coberto pelos dados históricos.
    5.  Registrar os sinais gerados, as apostas realizadas e os resultados obtidos (lucro ou prejuízo).
    6.  Analisar os resultados e calcular as métricas de performance (precisão, recall, F1-score, lucratividade).
    7.  Repetir os passos 2 a 6 com diferentes conjuntos de dados históricos e diferentes estratégias de apostas para otimizar os parâmetros do sistema.
*   **Benefícios:**
    *   Permite avaliar a performance do sistema sem arriscar dinheiro real.
    *   Ajuda a identificar pontos fortes e fracos do algoritmo.
    *   Permite otimizar os parâmetros do sistema (pesos do "score de intensidade", limiar para envio de sinais, etc.) para maximizar a lucratividade.
    *   Fornece insights sobre a robustez do sistema em diferentes condições de mercado.

## 9. Machine Learning (Futuro)

A integração de um modelo de Machine Learning, especificamente uma rede neural recorrente do tipo LSTM (Long Short-Term Memory), é um passo futuro planejado para aprimorar a capacidade preditiva do Blaze Predictor.

### 9.1 Modelo LSTM

*   **Justificativa:**  Redes neurais recorrentes, e em particular as LSTMs, são adequadas para analisar sequências de dados temporais, como o histórico de resultados do jogo "Double". Elas conseguem capturar padrões complexos e dependências de longo prazo nos dados, o que pode melhorar a precisão das previsões.
*   **Implementação:** O modelo LSTM será implementado usando uma biblioteca de aprendizado de máquina como TensorFlow ou Keras.
*   **Entrada do Modelo:**  O modelo receberá como entrada uma sequência de *n* rodadas anteriores (janela móvel), contendo informações como:
    *   Cor da casa (vermelho, preto, branco).
    *   Número da casa.
    *   Minuto da rodada.
    *   "Score de intensidade" calculado para cada minuto.
    *   Outras features que se mostrarem relevantes.
*   **Saída do Modelo:** O modelo produzirá como saída a probabilidade de cada cor (vermelho, preto e branco) ocorrer na próxima rodada.

### 9.2 Treinamento e Retreinamento

*   **Treinamento Inicial:** O modelo LSTM será treinado inicialmente com um grande conjunto de dados históricos coletados do *Tip Miner*.
*   **Retreinamento Periódico:** O modelo será retreinado periodicamente com novos dados coletados pelo `scraper.py`, permitindo que ele se adapte a mudanças nos padrões do jogo.
*   **Frequência de Retreinamento:** A frequência ideal de retreinamento será determinada experimentalmente, mas é provável que seja diária ou semanal.
*   **Treinamento Contínuo:**  O sistema será projetado para permitir o retreinamento contínuo do modelo em segundo plano, sem interromper a operação de coleta de dados e envio de sinais.

### 9.3 Validação e Testes

*   **Conjunto de Validação:**  Um conjunto de dados separado será usado para validar o modelo durante o treinamento e ajustar os hiperparâmetros.
*   **Conjunto de Teste:** Um conjunto de dados separado, não utilizado durante o treinamento ou validação, será usado para avaliar o desempenho final do modelo.
*   **Métricas de Avaliação:**  As seguintes métricas serão usadas para avaliar o modelo:
    *   Acurácia
    *   Precisão
    *   Recall
    *   F1-Score
    *   Lucratividade (simulada)
*   **Testes A/B:**  Diferentes versões do modelo (com diferentes arquiteturas ou hiperparâmetros) serão comparadas usando testes A/B para determinar a melhor configuração.

### 9.4 Integração com o Bot

*   **Previsões em Tempo Real:** O modelo treinado será integrado ao bot do Telegram para gerar previsões em tempo real.
*   **Combinação com o "Score de Intensidade":**  As previsões do modelo serão combinadas com o "score de intensidade" para gerar os sinais de aposta.
*   **Limiar de Confiança:** Um limiar de confiança será definido para determinar quando um sinal deve ser enviado. Esse limiar levará em conta tanto a probabilidade prevista pelo modelo quanto o "score de intensidade".

## 10. Considerações de Segurança

A segurança é uma preocupação primordial no desenvolvimento do Blaze Predictor, especialmente considerando que o sistema lida com informações sensíveis e potencialmente com transações financeiras.

### 10.1 Proteção do Token do Bot

*   **Armazenamento Seguro:** O token do bot do Telegram **nunca** deve ser armazenado diretamente no código-fonte. Ele será armazenado de forma segura em uma variável de ambiente, usando o arquivo `.env`.
*   **Acesso Restrito:** O arquivo `.env` será incluído no `.gitignore` para evitar que ele seja commitado acidentalmente no repositório Git.
*   **Revogação do Token:** Em caso de suspeita de comprometimento, o token do bot será imediatamente revogado e um novo token será gerado.

### 10.2 Segurança do Banco de Dados

*   **Acesso Local:** O banco de dados SQLite será usado apenas localmente e não será exposto a redes externas.
*   **Permissões de Arquivo:** As permissões do arquivo do banco de dados serão configuradas para restringir o acesso a usuários não autorizados.
*   **Backup Regular:** Backups regulares do banco de dados serão realizados para prevenir a perda de dados.
*   **Criptografia (Futuro):**  Em versões futuras, a criptografia do banco de dados poderá ser considerada para uma camada adicional de segurança.

### 10.3 Tratamento de Erros e Exceções

*   **Validação de Dados:** O `scraper.py` validará os dados coletados do *Tip Miner* para garantir que eles estejam no formato esperado e evitar erros de processamento.
*   **Tratamento de Exceções:**  O código incluirá blocos `try-except` para lidar com possíveis erros, como falhas de conexão, erros de análise HTML, erros de banco de dados, entre outros.
*   **Logs de Erros:**  Erros e exceções serão registrados em um arquivo de log para auxiliar na depuração e solução de problemas.
*   **Notificações de Falhas:** Em caso de falhas críticas, o sistema enviará uma notificação ao administrador (por exemplo, via Telegram ou e-mail).

## 11. Manutenção e Evolução

O Blaze Predictor será um sistema em constante evolução. A manutenção e o aprimoramento contínuos são essenciais para garantir sua precisão e confiabilidade a longo prazo.

### 11.1 Monitoramento Contínuo

*   **Logs:** O sistema registrará eventos importantes, erros e estatísticas de desempenho em arquivos de log.
*   **Monitoramento do "Score de Intensidade":** O "score de intensidade" será monitorado continuamente para identificar tendências e anomalias.
*   **Alertas:**  O sistema enviará alertas ao administrador em caso de falhas, erros ou comportamentos inesperados.

### 11.2 Atualização de Dependências

*   As dependências do projeto (bibliotecas Python) serão atualizadas regularmente para garantir compatibilidade, segurança e acesso a novas funcionalidades.
*   O processo de atualização será automatizado, sempre que possível, e incluirá testes para garantir que as atualizações não introduzam erros no sistema.

### 11.3 Refatoração de Código

*   O código será refatorado periodicamente para melhorar a legibilidade, a manutenibilidade e a performance.
*   Novas funcionalidades serão implementadas de forma modular, facilitando a expansão do sistema.

### 11.4 Adição de Novas Funcionalidades

*   **Melhorias na Previsão do Branco:**  A principal área de foco para futuras melhorias será a previsão do branco. Isso inclui a exploração de novos fatores para o "score de intensidade", a otimização dos pesos dos fatores existentes e a implementação de técnicas mais avançadas de Machine Learning.
*   **Previsão de Vermelho/Preto:**  O modelo LSTM será aprimorado para fornecer previsões mais precisas para as cores vermelha e preta.

*   **Interface Gráfica:**  Uma interface gráfica amigável poderá ser desenvolvida para facilitar a interação com o sistema e a visualização de dados.
*   **Análise de Tendências:**  O sistema poderá ser expandido para realizar análises mais aprofundadas das tendências do jogo, identificando padrões de comportamento da plataforma em diferentes horários e dias da semana.
*   **Gestão de Banca:**  Funcionalidades de gestão de banca poderão ser adicionadas para auxiliar o usuário a controlar seus investimentos e gerenciar seus riscos.

## 12. Considerações Finais

O Blaze Predictor é um projeto ambicioso e desafiador, que envolve a integração de várias tecnologias e técnicas complexas.  O sucesso do projeto depende de um desenvolvimento rigoroso, testes extensivos e um processo contínuo de monitoramento, aprendizado e aperfeiçoamento.

Este documento `README.md` serve como um guia detalhado para o desenvolvimento e a operação do Blaze Predictor. Ele será atualizado regularmente para refletir o estado atual do projeto, as decisões tomadas e os resultados obtidos.

A colaboração e o feedback contínuo entre os membros da equipe de desenvolvimento são essenciais para o sucesso deste projeto.  É fundamental manter uma comunicação clara e transparente, documentar todas as etapas do processo e realizar testes rigorosos para garantir a confiabilidade e a precisão do sistema.

**Este projeto representa uma oportunidade única de aplicar conhecimentos de engenharia de software, análise de dados e aprendizado de máquina para criar uma ferramenta inovadora e potencialmente lucrativa.**

## 13. Apêndice

### 13.1 Glossário de Termos

| Termo                 | Descrição                                                                                                                                                                                                                 |
| :-------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Branco              | O resultado alvo das nossas previsões. Representado pelo número 0 na roleta e paga 14x o valor da aposta.                                                                                                  |
| Puxadores            | As duas casas que saíram imediatamente antes e as duas casas que saíram imediatamente depois de um branco.                                                                                                                   |
| Minuto do Branco     | O minuto exato em que o branco ocorreu (ex: "07:03").                                                                                                                                                                       |
| Minuto Projetado     | O minuto calculado com base na soma do número do puxador com o minuto em que ele saiu.                                                                                                                                        |
| Taxa de Erro         | Uma tolerância de +/- 1 minuto em relação ao minuto projetado.                                                                                                                                                           |
| Convergência         | Quando dois ou mais puxadores, através de seus cálculos, apontam para o mesmo minuto projetado (dentro da taxa de erro).                                                                                                     |
| Minutos Águia        | Os números 4 e 11, que possuem um significado especial na análise e aumentam o "score de intensidade" quando presentes como puxadores.                                                                                      |
| "Espelho"           | Um padrão onde um branco ocorre em um minuto simétrico a um branco anterior (ex: branco em 00:59 e outro em 01:08, com diferença de 9 minutos, que é um número primo).                                                           |
| "Hora Boa"/"Hora Ruim" | Períodos do dia com maior ou menor frequência de brancos.                                                                                                                                                                   |
| "Recuperação da Casa" | Um período prolongado (mais de 15 minutos) sem a ocorrência de brancos, indicando que a plataforma pode estar ajustando os padrões para recuperar as perdas.                                                                  |
| "Mini-Chuva" de Brancos | Ocorrência de 4 ou mais brancos em um curto intervalo de tempo (5 ou 10 minutos).                                                                                                                                        |
| "Score de Intensidade" | Uma métrica numérica que quantifica a força de um sinal para a previsão de um branco em um determinado minuto.                                                                                                                |
| Web Scraping         | Processo de extração automatizada de dados de websites.                                                                                                                                                                    |
| LSTM                 | Long Short-Term Memory, um tipo de rede neural recorrente (RNN) adequada para analisar sequências de dados temporais.                                                                                                          |
| API                  | Application Programming Interface, um conjunto de regras e especificações que permite que diferentes softwares se comuniquem entre si.                                                                                           |
| MVP                 | Minimum Viable Product, a versão mais simples de um produto que pode ser lançada com um conjunto mínimo de funcionalidades, visando validar o conceito e obter feedback dos usuários.                                               |
| Backtesting          | Processo de simular a operação de um sistema em dados históricos para avaliar sua performance e lucratividade.                                                                                                                |
| Numerologia          | A prática de analisar a relação entre os números das casas e os minutos das rodadas para identificar padrões e prever a ocorrência do branco.                                                                               |

### 13.2 Tabela de Pesos do "Score de Intensidade"

| Fator                                         | Peso  |
| :-------------------------------------------- | :---- |
| **(A) Convergência de
| **(D) "Recuperação da Casa"**                | -8    |
| **(E) Minutos Águia (4 e 11)**                | +6    |
| **(F) Tempo desde o último branco**            | +3/+1/-2|
| **(G) Padrão do dia anterior**                | +2    |
| **(H) Soma dos puxadores > 20**                | +3    |
| **(I) Padrões de Cores**                     | +1    |
| **(J) "Espelho"**                               | +6    |
| **(K) Força Individual dos Puxadores**         | +1 a +5 |
| **(L) Combinação de Puxadores**                | +2 a +8 |
| **(M) Combinação de Puxadores com Minutos Águias** | +3 a +7 |

### 13.3 Exemplos Detalhados de Cálculo da Numerologia

### 13.4 Exemplos Detalhados de Fatores do "Score de Intensidade"

#### 13.4.1 Exemplo de Convergência

*   **Minuto do Branco:** 14:25
*   **Puxadores:**
    *   14:23 - 7 vermelho
    *   14:24 - 3 vermelho
    *   14:26 - 9 preto
    *   14:27 - 1 vermelho
*   **Cálculos:**
    *   14:23 + 7 = 14:30 (Taxa de erro: 14:29, 14:30, 14:31)
    *   14:24 + 3 = 14:27 (Taxa de erro: 14:26, 14:27, 14:28)
    *   14:26 + 9 = 14:35 (Taxa de erro: 14:34, 14:35, 14:36)
    *   14:27 + 1 = 14:28 (Taxa de erro: 14:27, 14:28, 14:29)
*   **Convergência:** Os minutos projetados 14:27, 14:28 e 14:29 (considerando a taxa de erro) convergem, indicando um sinal mais forte para esses minutos.
*   **Pontuação:** +3 pontos para convergência nesse caso, mas como o minuto do branco não foi encontrado, a pontuação seria desconsiderada.

#### 13.4.2 Exemplo de "Espelho"

*   **Branco 1:** 10:17
*   **Branco 2:** 10:38
*   **Diferença:** 38 - 17 = 21 minutos (não é primo)
*   **Espelho:** Não se aplica.

*   **Branco 1:** 09:52
*   **Branco 2:** 10:07
*   **Diferença:**  07 - 52 = 15 minutos (considerando a virada da hora)
*   **Espelho:** Não se aplica, pois 15 não é primo.

*   **Branco 1:** 00:59
*   **Branco 2:** 01:08
*   **Diferença:** 08 - 59 = 9 minutos (considerando a virada da hora)
*   **Espelho:** Aplicado com sucesso, pois a diferença é de 9 minutos, indicando uma simetria e reforçando a probabilidade de um branco no minuto 01:08.

#### 13.4.3 Exemplo de "Minutos Águia"

*   **Minuto do Branco:** 11:04
*   **Puxadores:**
    *   11:02 - 4 vermelho (Minuto Águia)
    *   11:03 - 9 preto
    *   11:05 - 12 preto
    *   11:06 - 6 vermelho
*   **Cálculo do Minuto Águia:** 11:02 + 2 = 11:04 (dentro da taxa de erro para 11:04)
*   **Pontuação:** +6 pontos, pois o puxador 4 é um Minuto Águia e contribui para a previsão do branco.

#### 13.4.4 Exemplo de "Mini-Chuva"

*   **Minuto em Análise:** 16:45
*   **Brancos nos últimos 10 minutos:** 16:36, 16:39, 16:42
*   **"Mini-Chuva":** Sim, 3 brancos em 10 minutos.
*   **Pontuação:** +5 pontos.

*   **Minuto em Análise:** 22:33
*   **Brancos nos últimos 5 minutos:** 22:29, 22:31
*   **"Mini-Chuva":** Sim, 2 brancos em 5 minutos.
*   **Pontuação:** +7 pontos.

#### 13.4.5 Exemplo de "Recuperação da Casa"

*   **Minuto em Análise:** 13:50
*   **Último Branco:** 13:19
*   **Tempo sem Branco:** 31 minutos
*   **"Recuperação da Casa":** Sim.
*   **Pontuação:** -8 pontos.

#### 13.4.6 Exemplo de "Tempo desde o último branco"

*   **Minuto em Análise:** 15:25
*   **Último Branco:** 15:08
*   **Tempo desde o último branco:** 17 minutos
*   **Pontuação:** +3 pontos, pois o último branco ocorreu há mais de 15 minutos.

#### 13.4.7 Exemplo de "Padrão do dia anterior"

*   **Minuto em Análise:**  11:05
*   **Brancos no dia anterior às 11:00-12:00:** 5
*   **Brancos no dia atual às 11:00-12:00 (até 11:05):** 4
*   **Diferença:** 1 branco.
*   **Pontuação:** +2 pontos, pois a diferença está dentro da tolerância de 2.

#### 13.4.8 Exemplo de "Soma dos puxadores"

*   **Minuto do Branco:** 18:30
*   **Puxadores:**
    *   18:28 - 10 preto
    *   18:29 - 5 vermelho
    *   18:31 - 11 preto (Minuto Águia)
    *   18:32 - 6 vermelho
*   **Soma dos Puxadores:** 10 + 5 + 11 + 6 = 32
*   **Pontuação:** +3 pontos, pois a soma é maior que 20.

### 13.5 Perguntas Frequentes (FAQ)

**1. O que é o Blaze Predictor?**

O Blaze Predictor é um sistema automatizado que tem como objetivo prever resultados no jogo "Double" da plataforma Blaze. Ele utiliza técnicas de web scraping, numerologia, um "score de intensidade" e, futuramente, Machine Learning para enviar sinais de alta confiança para apostas no branco.

**2. Como o Blaze Predictor funciona?**

O sistema coleta dados em tempo real do site *Tip Miner*, que fornece o histórico de resultados do "Double" da Blaze.  Ele então aplica as regras da numerologia para calcular os minutos projetados para a ocorrência do branco. Um "score de intensidade" é calculado para cada minuto, levando em consideração diversos fatores.  Quando o "score de intensidade" atinge um determinado limiar, o bot envia um sinal via Telegram.

**3. O que é a numerologia utilizada no Blaze Predictor?**

A numerologia é um conjunto de regras que analisa a relação entre os números das casas ("puxadores") que saem antes e depois de um branco e o minuto em que o branco ocorre. Ela é a base para a identificação de potenciais oportunidades de apostas.

**4. O que são os "puxadores"?**

Os puxadores são as duas casas que saíram imediatamente antes e as duas casas que saíram imediatamente depois de um branco.

**5. Como os minutos projetados são calculados?**

Para cada puxador, o número da casa é somado ao minuto em que ele saiu.  Uma taxa de erro de +/- 1 minuto é aplicada a cada minuto projetado.

**6. O que é o "score de intensidade"?**

O "score de intensidade" é uma métrica que quantifica a força de um sinal para a previsão de um branco em um determinado minuto. Ele é calculado com base em diversos fatores, como convergência de cálculos, "mini-chuvas" de brancos, frequência de brancos na hora, "recuperação da casa", minutos águia, tempo desde o último branco, padrão do dia anterior, soma dos puxadores, padrões de cores e "espelho".

**7. O que são "minutos águia"?**

"Minutos águia" são os números 4 e 11.  A presença deles como puxadores, especialmente no intervalo de minutos entre 58 e 08, é considerada um sinal forte e aumenta o "score de intensidade".

**8. O que é o "espelho"?**

O "espelho" é um padrão onde um branco ocorre em um minuto simétrico a um branco anterior. Por exemplo, um branco em 00:59 e outro em 01:08, com uma diferença de 9 minutos (um número primo).

**9. O que é uma "mini-chuva" de brancos?**

Uma "mini-chuva" de brancos é a ocorrência de 4 ou mais brancos em um curto intervalo de tempo (5 ou 10 minutos).

**10. O que significa "recuperação da casa"?**

"Recuperação da casa" é um período prolongado (mais de 15 minutos) sem a ocorrência de brancos, indicando que a plataforma pode estar ajustando os padrões para recuperar as perdas.

**11. O Blaze Predictor é garantia de lucro?**

Não. O Blaze Predictor é uma ferramenta de auxílio à tomada de decisão e não uma garantia de lucro. Apostas em jogos de azar envolvem riscos e os resultados não podem ser garantidos com 100% de certeza.

**12. O sistema é 100% automatizado?**

Atualmente, a coleta de dados e o cálculo do "score de intensidade" são automatizados. O envio de sinais via Telegram e a realização de apostas na Blaze ainda são etapas manuais, mas que serão automatizadas em futuras versões.

**13. Como posso contribuir para o projeto?**

Consulte a seção "Contribuições" neste `README.md`.

**14. Onde posso encontrar mais informações sobre o jogo "Double" da Blaze?**

Consulte o site oficial da Blaze e os termos e condições do jogo "Double".

**15. O que é e para que serve a função `_verificar_padrao_dia_anterior`?**

A função `_verificar_padrao_dia_anterior` é uma função auxiliar que foi desenvolvida para verificar se a quantidade de brancos em um determinado horário do dia atual é semelhante à quantidade de brancos que ocorreu no mesmo horário do dia anterior. Ela serve para identificar uma possível repetição de padrões diários### 13.5 Perguntas Frequentes (FAQ)

**1. O que é o Blaze Predictor?**

O Blaze Predictor é um sistema automatizado que tem como objetivo prever resultados no jogo "Double" da plataforma Blaze. Ele utiliza técnicas de web scraping, numerologia, um "score de intensidade" e, futuramente, Machine Learning para enviar sinais de alta confiança para apostas no branco, que paga 14x o valor da aposta.

**2. Como o Blaze Predictor funciona?**

O sistema coleta dados em tempo real do site *Tip Miner*, que fornece o histórico de resultados do "Double" da Blaze. Ele então aplica as regras da numerologia para calcular os minutos projetados para a ocorrência do branco. Um "score de intensidade" é calculado para cada minuto, levando em conta diversos fatores.  Quando o "score" atinge um determinado limiar, o bot (em desenvolvimento) envia um sinal via Telegram.

**3. O que é a numerologia utilizada no Blaze Predictor?**

A numerologia é um conjunto de regras que analisa a relação entre os números das casas ("puxadores") que saem antes e depois de um branco e o minuto em que o branco ocorre. Ela é a base para a identificação de potenciais oportunidades de apostas.

**4. O que são os "puxadores"?**

Os puxadores são as duas casas que saíram imediatamente antes e as duas casas que saíram imediatamente depois de um branco.

**5. Como os minutos projetados são calculados?**

Para cada puxador, o número da casa é somado ao minuto em que ele saiu. Uma taxa de erro de +/- 1 minuto é aplicada a cada minuto projetado.

**6. O que é o "score de intensidade"?**

O "score de intensidade" é uma métrica que quantifica a força de um sinal para a previsão de um branco em um determinado minuto. Ele é calculado com base em diversos fatores, como convergência de cálculos, "mini-chuvas" de brancos, frequência de brancos na hora, "recuperação da casa", minutos águia, tempo desde o último branco, padrão do dia anterior, soma dos puxadores, padrões de cores e "espelho".

**7. O que são "minutos águia"?**

"Minutos águia" são os números 4 e 11. A presença deles como puxadores, especialmente no intervalo de minutos entre 58 e 08, é considerada um sinal forte e aumenta o "score de intensidade".

**8. O que é o "espelho"?**

O "espelho" é um padrão onde um branco ocorre em um minuto simétrico a um branco anterior. Por exemplo, um branco em 00:59 e outro em 01:08, com uma diferença de 9 minutos (um número primo).

**9. O que é uma "mini-chuva" de brancos?**

Uma "mini-chuva" de brancos é a ocorrência de 2 ou mais brancos em um curto intervalo de tempo (5 ou 10 minutos).

**10. O que significa "recuperação da casa"?**

"Recuperação da casa" é um período prolongado (mais de 30 minutos) sem a ocorrência de brancos.

**11. O Blaze Predictor é garantia de lucro?**

Não. O Blaze Predictor é uma ferramenta de auxílio à tomada de decisão e não uma garantia de lucro. Apostas em jogos de azar envolvem riscos e os resultados não podem ser garantidos com 100% de certeza.

**12. O sistema é 100% automatizado?**

Atualmente, a coleta de dados e o cálculo do "score de intensidade" são automatizados. O envio de sinais via Telegram e a realização de apostas na Blaze ainda são etapas manuais, mas que serão automatizadas em futuras versões.

**13. Como posso contribuir para o projeto?**

Consulte a seção "Contribuições" neste `README.md`.

**14. Onde posso encontrar mais informações sobre o jogo "Double" da Blaze?**

Consulte o site oficial da Blaze e os termos e condições do jogo "Double".

**15. Como o "padrão do dia anterior" é verificado?**

A função `_verificar_padrao_dia_anterior` compara a quantidade de brancos que saíram em um determinado intervalo de horário do dia atual com a quantidade de brancos que saíram no mesmo intervalo de horário do dia anterior. Se a diferença for menor ou igual a 2, o "score de intensidade" é incrementado.

**16. Como a "soma dos puxadores" influencia o "score de intensidade"?**

A função `_verificar_soma_puxadores` calcula a soma dos números dos quatro puxadores de um branco. Se essa soma for maior que 20, o "score de intensidade" é incrementado, pois isso indica uma possível tendência.

**17. O que são os "padrões de cores" e como eles são considerados?**

Os "padrões de cores" referem-se à sequência de cores que antecedem um branco. A implementação atual considera sequências de 3 ou mais cores iguais (vermelho ou preto) e incrementa o "score de intensidade" em 1 ponto.  Padrões mais complexos serão analisados e implementados em futuras versões.

**18. Como o "espelho" é identificado?**

A função `_verificar_espelho` busca por brancos que tenham ocorrido em minutos simétricos em relação ao minuto atual. A diferença entre os minutos deve ser um número primo para ser considerado um "espelho".

**19. Qual a linguagem de programação utilizada?**

Python é a linguagem principal utilizada no projeto, devido à sua versatilidade, ampla gama de bibliotecas para análise de dados e aprendizado de máquina, e facilidade de integração com o Telegram.

**20. Onde os dados são armazenados?**

Os dados coletados e as análises são armazenados em um banco de dados SQLite local (`blaze_predictor.db`).

**21. Quais são os riscos de usar o Blaze Predictor?**

Como qualquer ferramenta de auxílio à decisão em apostas, o Blaze Predictor não elimina os riscos inerentes ao jogo.  Os resultados passados não garantem resultados futuros, e a plataforma Blaze pode alterar seus algoritmos a qualquer momento. É fundamental usar o sistema com responsabilidade e gerenciar a banca de forma consciente.

**22. O que é feito para garantir a segurança do bot do Telegram?**

O token do bot é armazenado em uma variável de ambiente e não deve ser compartilhado.  Além disso, o bot será configurado para aceitar comandos apenas de usuários autorizados.

**23. Como posso executar o projeto?**

As instruções detalhadas para executar o projeto estão na seção 7 deste `README.md`.

**24. Quais são as próximas etapas de desenvolvimento?**

As próximas etapas incluem a implementação completa das funções auxiliares do "score de intensidade", a integração com o bot do Telegram, o desenvolvimento do modelo LSTM e a realização de testes extensivos.

**25. O projeto é de código aberto?**

Sim, o projeto é de código aberto e está disponível no GitHub.  A licença e as diretrizes para contribuição estão detalhadas neste `README.md`.

## 14. Contribuições

Contribuições para o projeto Blaze Predictor são bem-vindas. Se você deseja contribuir, por favor siga os passos abaixo:

1.  **Fork o repositório:** Crie uma cópia do repositório Blaze Predictor na sua conta do GitHub.
2.  **Clone o repositório:** Clone o fork que você criou para a sua máquina local.
3.  **Crie uma branch:** Crie uma nova branch para a sua contribuição. Use um nome descritivo que reflita a alteração que você está propondo.
4.  **Faça as alterações:** Implemente as suas alterações no código, seguindo as diretrizes de estilo e boas práticas do projeto.
5.  **Escreva testes:** Se você adicionar novas funcionalidades ou corrigir bugs, certifique-se de escrever testes unitários para garantir a qualidade do código.
6.  **Execute os testes:** Execute todos os testes unitários para garantir que as suas alterações não quebraram nenhuma funcionalidade existente.
7.  **Documente as alterações:** Atualize a documentação (incluindo este README) para refletir as suas alterações.
8.  **Faça commit das alterações:** Faça commit das suas alterações com mensagens de commit claras e descritivas.
9.  **Crie um Pull Request:** Crie um Pull Request do seu fork para o repositório principal do Blaze Predictor.
10. **Aguarde a revisão:** Os mantenedores do projeto irão revisar o seu Pull Request e, se tudo estiver certo, ele será mesclado ao repositório principal.

**Diretrizes para Contribuição:**

*   Siga as convenções de codificação do projeto.
*   Escreva testes unitários para todas as novas funcionalidades e correções de bugs.
*   Mantenha a documentação atualizada.
*   Seja respeitoso e profissional em todas as interações com outros contribuidores e mantenedores.

## 15. Licença

Este projeto é licenciado sob a [MIT License](LICENSE) (ou outra licença de sua escolha). Veja o arquivo `LICENSE` para mais detalhes.

## 16. Contato

Para entrar em contato com a equipe de desenvolvimento do Blaze Predictor, envie um e-mail para [seu email aqui].

## 17. Changelog

### 17.1 Versão 0.1.0 (MVP) - [Data de Lançamento]

*   Implementação da coleta de dados em tempo real do *Tip Miner*.
*   Armazenamento dos dados em banco de dados SQLite.
*   Cálculo da numerologia e do "score de intensidade".
*   Funções de teste unitário para as principais funcionalidades.
*   Integração do "score de intensidade" ao processo de coleta de dados.

### 17.2 Versão 0.2.0 (Futura)

*   Implementação do bot do Telegram.
*   Envio de sinais de aposta com base no "score de intensidade".

### 17.3 Versão 0.3.0 (Futura)

*   Integração do modelo LSTM para previsão de vermelho/preto.

### 17.4 Versão 0.4.0 (Futura)

*   Aprimoramento do modelo LSTM para previsão do branco.

## 18. Disclaimer

O Blaze Predictor é um projeto experimental e em desenvolvimento. Os resultados obtidos pelo sistema não devem ser interpretados como garantia de lucro e não nos responsabilizamos por possiveis perdas em apostas. A utilização do sistema é de total responsabilidade do usuário. Apostas em jogos de azar envolvem riscos e devem ser feitas com cautela e responsabilidade.

---

**Fim do `README.md`**


Estou pronto para continuar ajudando você com a implementação e os testes.  Vamos em frente!
