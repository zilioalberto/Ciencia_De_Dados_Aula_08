# Ciencia_De_Dados_Aula_08
# Aula 08 
# Alunos:
# Alberto Zilio
# Roni Pereira

## 1) Features escolhidas e justificativas (com base na EDA da Aula 07)
- **Contract**: contratos *month-to-month* apresentaram maior proporção de churn do que planos de 1 ou 2 anos; forte poder explicativo.
- **tenure**: clientes com pouco tempo de casa cancelam mais; variável crítica para retenção.
- **MonthlyCharges**: mensalidades mais altas associam-se a maior churn; captura sensibilidade a preço.
- **InternetService**: *Fiber optic* costuma ter churn maior do que *DSL* e *No*; diferencia perfil de serviço.
- **Partner**: ausência de parceiro correlacionou com churn maior; proxy de perfil/demanda do domicílio.
- **PaymentMethod**: *Electronic check* concentrou churn acima da média; sinal útil de risco.

> Optamos por **não** incluir variáveis irrelevantes (p.ex., identificadores) e potenciais redundâncias, para reduzir ruído, evitar multicolinearidade desnecessária e favorecer generalização.

---

## 2) Por que precisamos de One-Hot Encoding nas categóricas?
Modelos não “entendem” rótulos como “Month-to-month” ou “DSL”. O *one-hot* cria colunas binárias (0/1) por categoria, permitindo que o algoritmo aprenda efeitos específicos de cada categoria. Usamos `drop_first=True` para manter uma categoria como **referência**, evitando colinearidade perfeita.  
**Interpretação:** 1 = o registro **pertence** àquela categoria; 0 = **não pertence** (logo, está em outra, possivelmente a de referência).

---

## 3) O que é a divisão treino-teste e por que é importante?
Separamos os dados em **treino (70%)** e **teste (30%)** com **estratificação** por *Churn*, preservando a proporção de classes. O conjunto de **teste** simula dados “nunca vistos” e oferece uma **avaliação honesta**. Sem essa separação, poderíamos superestimar o desempenho do modelo (*overfitting*).

---

## 4) Nosso modelo é melhor do que sempre prever “Não Churn”?
- **Acurácia (teste):** **78.04%**
- **Baseline (sempre prever a classe majoritária):** **73.47%**

**Interpretação:** Como a acurácia do modelo supera a *baseline* com margem, ele está capturando **padrões reais** além do simples “chute” da maioria. Como o problema é **desbalanceado**, também consideramos **Precision/Recall/F1** da classe **Yes (churn)** para avaliar utilidade prática (ex.: priorizar *Recall* se o custo de perder um churn verdadeiro for alto).

---

## 5) O que significa escolher `max_depth=4`? Aumentar para 10 melhora?
`max_depth` controla a **complexidade** da árvore. Aumentar a profundidade **não garante** melhor desempenho em **teste**: após certo ponto, o modelo tende a “decorar” o treino (**overfitting**), piorando a generalização.  
Na nossa varredura de profundidades (*grid*), o melhor desempenho em teste ocorreu em: **max_depth = 5** (acurácia de teste **78.32%**).  
**Gap treino–teste:** **1.88 p.p.** (acc_treino = 80.20% vs. acc_teste = 78.32%) — se muito alto, é indício de overfitting; considerar reduzir profundidade e/ou ajustar `min_samples_leaf`.

---

## 6) Conclusão Final (modelo e negócio)
- O modelo de **Árvore de Decisão** com as features selecionadas apresentou **acurácia de teste** acima da *baseline* (**78.04%** vs. **73.47%**), indicando ganho real sobre “sempre No”.  
- Métricas por classe sugerem que o modelo **[captura bem / precisa melhorar]** casos de **churn (Yes)** — avaliar **Recall/Precision/F1** conforme o custo de erro no negócio.  
- **Próximos passos recomendados:** refinar hiperparâmetros (`max_depth`, `min_samples_leaf`), testar **balanceamento de classes** e comparar com modelos como **Regressão Logística** e **Random Forest**.  
- **Uso prático:** as regras da árvore ajudam a **explicar** fatores de risco (ex.: contratos mensais + mensalidades altas + fibra), apoiando ações de **retenção** direcionadas.
