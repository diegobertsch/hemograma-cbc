# Análisis de Anemia a partir de Hemograma Completo (CBC)

## Contexto
Análisis exploratorio de un dataset de hemogramas de 364 pacientes adultos 
(Eureka Diagnostic Center, Lucknow, India, sep-dic 2020), con el objetivo de 
identificar patrones de prevalencia, tipo morfológico y severidad de anemia.

## Datos
Fuente: Kaggle, "Anemia Diagnosis Dataset - Complete Blood Count".
12 variables de hemograma (RBC, PCV, MCV, MCH, MCHC, RDW, TLC, plaquetas, HGB) 
más edad y sexo.

## Licencia del dataset: CC0 (dominio público).

**Nota sobre los datos:** la descripción original indica exclusión de menores 
de 15 años, pero el dataset contiene pacientes desde los 11 años — inconsistencia 
detectada durante el control de calidad. La codificación de la variable `Sex` 
(0/1) no está documentada en la fuente; se infirió 0=varón, 1=mujer a partir 
de las medianas de hemoglobina por grupo (13.1 vs 11.1 g/dL), consistente con 
valores fisiológicos esperados.

## Metodología
1. Limpieza (encabezados, fila descriptiva, filas vacías, detección de outliers 
   en MCHC)
2. Diagnóstico de anemia según puntos de corte OMS (HGB < 13 varones, < 12 mujeres)
3. Clasificación morfológica por MCV (microcítica/normocítica/macrocítica)
4. Clasificación de severidad (leve/moderada/grave)

## Hallazgos principales
- Prevalencia de anemia: 57.1% (208/364) — alta porque es población de centro 
  diagnóstico, no muestra poblacional general
- La anemia microcítica concentra los casos más severos (66% moderada/grave)
- La anemia normocítica es mayormente leve o ausente
- La prevalencia aumenta con la edad, marcadamente en el grupo 71-89 (74%)

## Modelado

Se evaluó un modelo de regresión logística para predecir anemia usando 
variables del hemograma, con validación cruzada estratificada (5 folds).

**Nota metodológica importante:** se excluyeron del set de variables la 
hemoglobina (HGB, define el diagnóstico) y sus derivados algebraicos directos 
(PCV, MCH, RBC), que producían un ROC-AUC artificialmente alto (~0.97) por 
fuga de datos indirecta. Con variables genuinamente independientes 
(MCV, MCHC, RDW, TLC, PLT, Age, Sex), el ROC-AUC real es de 0.68 ± 0.07.

Este resultado, más modesto, es clínicamente coherente: sin mirar la 
hemoglobina o sus derivados directos, el hemograma completo aporta señal 
real pero limitada para predecir anemia. Las variables con mayor peso 
(RDW alto, MCV bajo) son consistentes con el patrón esperado en anemia 
ferropénica.

## Herramientas
Python, pandas, numpy, matplotlib, seaborn, scikit-learn

## Estado del proyecto y próximos pasos
Análisis exploratorio y modelo base completos. Como próximo paso, se podría 
comparar contra un árbol de decisión y evaluar el desempeño por subtipo 
morfológico de anemia.