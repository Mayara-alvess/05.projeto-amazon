
# Ficha Técnica: Projeto Análise de Dados 
## Título do Projeto: Validação de Hipóteses na  Avaliação de Produtos.

### Objetivo 

- Esta análise busca identificar os principais fatores que impactam a classificação de produtos na Amazon, com foco na relação entre preço com desconto, número de avaliações e classificação dos produtos. O objetivo é fornecer insights para otimizar estratégias de vendas e aumentar a satisfação do cliente
  # Processamento e Análises
 
  **Limpeza dos dados**
    
    **Tabela “origem_produto”**
    
    Contém 1469 linhas
    
    Identifiquei :
    
    4 nulos na variável “ABOUT_PRODUCT”
    
    Optei por excluir esses nulos.
    
    Duplicados:
    
    96 duplicados na variável “PRODUCT_ID”
    
    4 duplicados na variável “RATING_COUNT”
    
    Optei por excluir os duplicados e deixar somente os ids únicos.
    
    **Tabela “origem_review”**
    
    Contém 1465 linhas
    
    Identifiquei:
    
    468 nulos na variável “IMG_LINK”
    
    466 nulos na variável “PRODUCT_LINK”
    
    2 nulos na variável “RATING_COUNT”
    
    Optei por excluir as colunas de img_link e product_link, por entender que não irei utilizar na análise e retirar os 2 nulos na coluna rating_count, por entender que não irá comprometer a análise.
    
    Duplicados:
    
    92 duplicados na variável “PRODUCT_ID”
    
    Optei por excluir os duplicados e deixar somente os ids únicos.
    
    **Tabela “REVIEW” varíaveis:**
    
    “USER_NAME”
    
    “REVIEW_TITLE”
    
    “REVIEW_CONTENT”
    
    **Tabela “PRODUTO” varíaveis:**
    
    “PRODUCT_NAME”
    
    “CATEGORY”
    
    “ABOUT_PRODUCT” 
    
    Foi feita a limpeza nessas variaveis, continham dados discrepantes.
  
    Transformação tipo de variável STRING para FLOAT64.
    
    “RATING”
    
    Alteração nome das categorias:
    
    | Antes | Depois |
    | --- | --- |
    | Car | Outros segmentos |
    | Health | Outros segmentos |
    | HomeImprovement | Outros segmentos |
    | MusicalInstruments | Outros segmentos |
    | Toys | Outros segmentos |
    | Home | Home&Kitchen |
    | OfficeProducts’ | Office Products |
    
   As demais categorias foram mantidas os mesmos nomes.
    
   Criação da nova varíavel “grau_satisfação” criada a partir da varíavel “NEW_RATING”

## União das tabelas
  
As tabelas foram unificadas em uma única tabela denominada "TABELA_UNIFICADA". Após a unificação, a tabela foi carregada no Google Colab, onde foram realizadas análises de sentimento e regressão linear.


 ## Ferramentas e Tecnologias

(linguagem SQL): para gerenciamento de dados.

Google Colab

PowerBI

Linguagem: Python
  
 # Resultados e Conclusões

<details>
  <summary><strong>Hipótese 1 | Quanto maior o desconto, melhor será a pontuação.</strong></summary>

  Correlação: -0.14982380175314391  
  Valor p: 3.259031128486439e-08  
  A correlação negativa fraca indica que produtos com maior desconto tendem a ter uma classificação um pouco baixa. O valor p indica que é estatisticamente significativa, portanto rejeitamos H0 e, embora a correlação seja fraca, há uma relação negativa entre a porcentagem de desconto e a classificação do produto.

</details>

<details>
  <summary><strong>Hipótese 2 | Quanto maior o número de pessoas que avaliaram o produto, melhor será a classificação.</strong></summary>

  Correlação: 0.19325398701303323  
  Valor p: 8.28686412616828e-13

  A correlação entre a classificação do produto e o número de pessoas que votaram é relativamente baixa, indicando uma associação fraca entre essas duas variáveis. No entanto, o valor p sugere que, apesar da correlação ser fraca, há uma diferença estatisticamente significativa entre as classificações dos produtos com diferentes quantidades de avaliações. Isso indica que produtos com diferentes volumes de avaliações tendem a ter classificações significativamente distintas.

  Rejeitamos H0. Há uma correlação significativa.

</details>

<details>
  <summary><strong>Hipótese 3 | Quanto maior o número de avaliações positivas utilizando a variável (score de sentimento), melhor será a classificação.</strong></summary>

  Correlação: 0.2471604419349042  
  Valor p: 3.2809684251036406e-20

  De maneira geral, uma correlação de 0,24 é considerada fraca, mas ainda indica que, quando a classificação do sentimento do cliente em relação ao produto aumenta, a pontuação do produto também tende a aumentar. Além disso, com o valor p baixo, menor que 0.05, rejeitamos a hipótese nula H₀, o que confirma que há uma diferença estatisticamente significativa entre o score de sentimento e a classificação do produto.

</details>

# Links de interesse
[Google Colab](https://colab.research.google.com/drive/1tmpj2CS6TZUzXqX4slesSBPdm9EKSn-O?authuser=1#scrollTo=h6Z2fGlg1qpM)  
[Documentação detalhada](https://www.notion.so/Documenta-o-amazon-058271a3b2e14f8d88480c6343c59821)
