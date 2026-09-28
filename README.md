# Treatment-Resistant Schizophrenia Prediction with Machine Learning

Projecte desenvolupat per a l'assignatura **Introducció a l'Aprenentatge Automàtic (IAA)** del Grau en Intel·ligència Artificial de la UPC.

L'objectiu és desenvolupar un sistema de classificació capaç de predir si un pacient presenta **esquizofrènia resistent al tractament (Treatment-Resistant Schizophrenia, TRS)** o esquizofrènia no resistent al tractament (NTRS) a partir de dades clíniques multimodals.

El projecte cobreix el pipeline complet d'un problema de Machine Learning: anàlisi exploratòria, preprocessament, selecció de variables, entrenament, cerca d'hiperparàmetres, validació creuada, comparació de models i documentació del model final.

> **Disclaimer:** Aquest projecte té finalitats exclusivament acadèmiques i educatives. El model no està dissenyat ni validat per a ús clínic o diagnòstic.

## Objectiu

El problema es formula com una tasca de **classificació binària**:

- `0` — Non-Treatment-Resistant Schizophrenia (NTRS)
- `1` — Treatment-Resistant Schizophrenia (TRS)

Les dades inclouen diferents tipus d'informació:

- variables demogràfiques,
- informació clínica,
- resultats de laboratori,
- marcadors genètics,
- biomarcadors,
- i variables derivades de neuroimatge.

L'objectiu no és només maximitzar l'accuracy, sinó aconseguir un rendiment equilibrat entre les dues classes.

---

## Dataset

El conjunt d'entrenament conté **9.000 mostres** i presenta un desbalanceig considerable de la variable objectiu:

- NTRS: aproximadament **68.5%**
- TRS: aproximadament **31.5%**

Per aquest motiu, la mètrica principal utilitzada durant el desenvolupament és el **F1-macro**, que dona el mateix pes a les dues classes independentment de la seva freqüència.

També s'utilitzen:

- particions estratificades,
- cross-validation estratificada,
- i pesos de classe quan l'algoritme ho permet.

> Les dades originals no s'inclouen en aquest repositori perquè van ser proporcionades específicament per a la pràctica acadèmica.

---

## Preprocessament

El preprocessament es realitza mitjançant **pipelines de scikit-learn** per assegurar que totes les transformacions s'ajusten exclusivament sobre les dades d'entrenament de cada fold i evitar **data leakage**.

El pipeline inclou:

- imputació de valors absents mitjançant la mediana,
- normalització de variables numèriques amb `StandardScaler`,
- codificació de variables categòriques amb `OneHotEncoder`,
- eliminació d'una variable altament correlacionada,
- i particionament estratificat.

També es van estudiar els outliers mitjançant el criteri IQR. Es va decidir no eliminar-los de manera general, ja que podien representar informació clínica rellevant.

---

## Exploratory Data Analysis

Durant l'anàlisi exploratòria es van estudiar:

- distribució de les classes,
- missing values,
- outliers,
- variables categòriques i binàries,
- correlacions entre variables numèriques,
- i dimensionalitat de les dades.

Es va aplicar **PCA** com a eina d'anàlisi per estudiar la variància explicada. Aproximadament 15 components permetien explicar el 95% de la variància, però finalment no es va aplicar reducció dimensional per preservar la interpretabilitat de les variables.

---

## Models

Es van implementar, entrenar i comparar diferents algoritmes de Machine Learning.

### Support Vector Machine

Es van explorar diferents:

- kernels (`linear`, `RBF`, `polynomial`),
- valors de `C`,
- valors de `gamma`,
- graus polinomials,
- i estratègies de ponderació de classes.

La millor configuració va utilitzar un **kernel RBF** i `class_weight="balanced"`.

### XGBoost

Es van ajustar hiperparàmetres relacionats amb:

- nombre d'arbres,
- profunditat,
- learning rate,
- subsampling,
- selecció de variables,
- regularització L1/L2,
- i pes de la classe minoritària.

També es va estudiar específicament l'overfitting del model.

### Custom Logistic Regression

Es va implementar **des de zero una regressió logística compatible amb l'API de scikit-learn**.

El model inclou:

- funció sigmoide,
- entrenament mitjançant gradient descent,
- mini-batches,
- regularització L2,
- i suport per a `class_weight="balanced"`.

Aquesta implementació permet integrar el model dins dels pipelines i les eines de cross-validation de scikit-learn.

### Explainable Boosting Machine

També es va experimentar amb un **Explainable Boosting Machine (EBM)** com a alternativa interpretable basada en boosting.

### Random Forest

Finalment, es va entrenar un Random Forest com a model addicional per comparar el seu rendiment i comportament davant l'overfitting.

---

## Model Selection

Els principals models van obtenir els següents resultats:

| Model | F1-macro Train CV | F1-macro Validation CV | F1-macro Test |
|---|---:|---:|---:|
| Custom Logistic Regression | 0.5980 | **0.5960** | **0.5832** |
| SVM | 0.5938 | 0.5923 | 0.5729 |
| XGBoost | 0.6410 | 0.5801 | 0.5658 |
| Random Forest | 0.8333 | 0.5878 | 0.5687 |
| EBM | 0.5125 | 0.4856 | 0.4823 |

La **Custom Logistic Regression** es va seleccionar com a model final.

Tot i ser un model relativament senzill, va obtenir el millor F1-macro sobre el conjunt de test i va mostrar una diferència mínima entre el rendiment de train i validació durant cross-validation, indicant una bona estabilitat i absència de senyals importants d'overfitting.

---

## Model Card

El model final també es va documentar mitjançant una **Model Card** que recull:

- objectiu del model,
- dades utilitzades,
- pipeline de preprocessament,
- mètriques d'avaluació,
- matriu de confusió,
- corbes d'avaluació,
- limitacions,
- i consideracions d'ús.

El projecte és estrictament acadèmic i el model **no ha de ser utilitzat per prendre decisions mèdiques o clíniques**.

---

## Tecnologies

- Python
- NumPy
- pandas
- scikit-learn
- XGBoost
- InterpretML
- VerifyML
- Matplotlib
- Seaborn
- Jupyter Notebook

---

## Estructura del repositori

```text
.
├── README.md
├── trs_prediction.ipynb
├── report.pdf
├── requirements.txt
└── results/
    └── submission.csv
```

El notebook principal conté el pipeline complet:

1. Exploratory Data Analysis
2. Preprocessament
3. Anàlisi i selecció de variables
4. Cross-validation
5. Hyperparameter tuning
6. Entrenament i comparació de models
7. Implementació de la regressió logística
8. Selecció del model final
9. Model Card
10. Generació de prediccions

---

## Principals aprenentatges

Aquest projecte va permetre treballar de manera pràctica diferents aspectes d'un pipeline complet de Machine Learning:

- anàlisi exploratòria de dades,
- tractament de missing values i outliers,
- gestió de datasets desbalancejats,
- prevenció de data leakage,
- pipelines de preprocessament,
- feature analysis,
- PCA,
- cross-validation,
- hyperparameter tuning,
- implementació d'algoritmes des de zero,
- comparació de models,
- detecció d'overfitting,
- selecció de mètriques adequades,
- i documentació responsable de models de Machine Learning.
