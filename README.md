# 🏘️ MVP - Identificação de Oportunidades no Mercado de Leilões Imobiliários

## 🎯 Objetivo

Este projeto tem como objetivo desenvolver um pipeline de dados na nuvem, utilizando a plataforma Databricks, com foco na **identificação de imóveis arrematados em leilões com alto potencial de lucro na revenda**. A estratégia consiste na **comparação entre o valor de mercado do metro quadrado** e:

- O valor de avaliação bancária;
- O valor de arrematação registrado nos leilões judiciais ou extrajudiciais.

A intenção é fornecer suporte à **tomada de decisão por parte de investidores**, sinalizando os bairros e perfis de imóveis com maior potencial de retorno financeiro estimado no curto prazo (até 12 meses).

---

## ❓ Perguntas de Negócio e Respostas

1. **Em quais bairros há maior diferença percentual entre o valor de mercado por m² e o valor de avaliação?**  
   R: Centro e Campo Grande apresentaram os maiores gaps entre valor de avaliação e valor de mercado, indicando avaliações superestimadas.

2. **Em quais bairros há maior diferença percentual entre o valor de mercado por m² e o valor final de arrematação?**  
   R: Santa Cruz se destacou com alguns imóveis arrematados com até 60% de desconto real sobre o valor de mercado.

3. **Quais perfis de imóveis apresentam maior potencial de desconto real?**  
   R: Imóveis com 2 quartos e área útil entre 40 e 60 m² apresentaram os maiores descontos percentuais.

4. **Existe correlação entre o desconto anunciado nos leilões e o desconto real calculado por m²?**  
   R: Não. A análise de dispersão mostrou diversos casos em que o desconto anunciado era alto, mas o preço estava acima do mercado.

5. **Quais imóveis arrematados se destacam como grandes oportunidades de negócio?**  
   R: Foi gerado um DataFrame com os imóveis com maiores descontos reais, cruzando com seus respectivos links para consulta direta.

---

## 📦 Fontes de Dados

- **Leilões da Caixa Econômica Federal** (coletados via planilhas públicas).
- **Valores de mercado** obtidos manualmente via anúncios no site VivaReal (regiões: Centro, Campo Grande e Santa Cruz - RJ).

> ❗ Devido a proteções contra scraping em portais como Zap Imóveis e OLX, foi necessário realizar a coleta do valor de mercado por meio de extração manual de anúncios com informações visuais.

---

## 🔄 Pipeline de Dados

O projeto foi implementado inteiramente na plataforma **Databricks Community Edition**, utilizando Apache Spark e Python.

### 🔍 1. Coleta
- Base principal: imóveis em leilão (leilões da Caixa).
- Complemento: anúncios do mercado imobiliário local (VivaReal).

### 🧱 2. Modelagem
- Modelo flat (Data Lake) com duas fontes principais:
  - `df_completo`: imóveis de leilão, com ID, bairro, área, valor de avaliação, preço de arrematação e link.
  - `df_mercado_limpo`: imóveis de mercado, com bairro, área e preço.
- Catálogo de dados com descrição dos campos e tipos (fornecido no notebook).

### ⚙️ 3. Carga
- Dados tratados e persistidos em **formato Parquet**, organizados por checkpoints para facilitar reuso.

### 📊 4. Análise

#### a. Qualidade dos dados
- Verificação e remoção de valores nulos.
- Normalização de nomes de bairros para cruzamento de dados.
- Cálculo de métricas de dispersão, média e outliers por bairro e área útil.

#### b. Solução do problema
- Cálculo do **valor de mercado por m²** por bairro.
- Cálculo do **valor de leilão por m²**.
- Cálculo do **desconto real percentual**:  
  Desconto real = (valor de mercado - valor de leilão) / valor de mercado*100
- Comparação entre o **desconto anunciado** (fornecido na base da Caixa) e o **desconto real** obtido com base nos preços de mercado.
- Visualização dos resultados com gráficos de dispersão e boxplots.

---

## 📌 Principais Conclusões

- A maioria dos imóveis apresenta **desconto real inferior ao anunciado**, indicando possíveis distorções nos valores de avaliação.
- Imóveis com **maior área útil** tendem a ter maior variação de preço por m² e podem esconder boas oportunidades.
- A **zona oeste do Rio de Janeiro** (Campo Grande, Santa Cruz e Centro) concentra imóveis com maior potencial de retorno.
- Há muitos casos com **desconto real negativo**, o que indica que o valor de avaliação pode estar inflado ou que ainda estão em fases iniciais de leilão.

---

## 🔭 Visão Futura

Para uma evolução do projeto, pretende-se:
- Automatizar a coleta de preços de mercado via OCR e NLP a partir de screenshots de portais.
- Adicionar análise de risco jurídico com extração de informações de editais.
- Expandir a cobertura para outras cidades e tipos de imóvel.
- Incluir uma métrica de "risco x retorno".

---

## 📁 Organização

- `notebook_MVP_Leilao`: desenvolvimento completo do pipeline em Databricks
- `/FileStore/tables`: dados em formato Parquet
- `README.md`: descrição completa do projeto
- `resultados.png`: visualizações principais (gráficos)

---

## ✍️ Autoavaliação
Tive muita dificuldade com os códigos, me tomou muito tempo descobrir os comandos para cada tarefa e muito mais tempo para remover os erros. Esse trabalho foi um verdadeiro desafio.

No inicio não imaginei que teria tantas barreiras para fazer scraping de dados, entretanto acredito que o objetivo proposto foi amplamente atendido. Consegui construir um pipeline funcional na nuvem com coleta, modelagem, carga e análise de dois conjuntos de dados distintos. Embora a coleta automatizada de dados de mercado não tenha sido possível devido a restrições de scraping, contornamos isso com extração manual via screenshots, permitindo manter a qualidade e integridade da análise.

As maiores dificuldades foram técnicas, relacionadas a barreiras de scraping impostas pelos sites e ao tempo limitado para coleta manual. Espero que a solução encontrada tenha atendido o contexto do MVP.

Como trabalho futuro, fica o objetivo de automatizar a coleta com OCR ou API confiável, expandir a cobertura geográfica e realizar a análise de risco jurídico de cada imóvel, aumentando a utilidade prática da ferramenta como apoio ao investimento em leilões.
