# Challenge_Telecom-X_Parte-2
Análisis de evasión de clientes

## **1) Resumen ejecutivo**

**Mejor modelo:** Regresión Logística (seleccionada como best_model = lr_best_model).

**Rendimiento en test:**

**Precision** ≈ 0.735

**Recall** ≈ 0.805

**F1** ≈ 0.768

**ROC AUC** ≈ 0.836

**Drivers clave de cancelación** (según variables seleccionadas y el mejor modelo):

**Tipo de contrato:** los contratos mes a mes elevan el riesgo; Two year reduce fuertemente la cancelación.

**Tenure (antigüedad):** menor tenure = mayor probabilidad de cancelar; tenure alto protege.

**Servicios de seguridad/soporte:** sin OnlineSecurity y/o sin TechSupport incrementa el churn; contar con ellos lo reduce.

**InternetService:** perfiles (p. ej., Fiber optic) suelen mostrar más cancelación si no van acompañados de valor percibido/soporte.

**PaymentMethod:** métodos como electronic check suelen asociarse a mayor churn; pagos automáticos tienden a estabilizar.

**PaperlessBilling:** según combinación con método de pago/contrato, puede asociarse con mayor o menor riesgo (suele elevar riesgo si va con electronic check).

**SeniorCitizen:** muestra señal de mayor risk (necesita atención y servicio adaptado).

**Total (gasto):** tickets altos sin contraprestación percibida aumentan el riesgo.


## 2) Datos y variables utilizadas

Selección aplicada en el preprocesamiento (one-hot encoding para categóricas, estandarización para numéricas):

**Numéricas:** SeniorCitizen, tenure, OnlineSecurity, TechSupport, PaperlessBilling, Total.

**Categóricas (OHE):** InternetService, Contract, PaymentMethod.

Se detectó desbalance y se aplicó downsampling de la clase mayoritaria previo al modelado.

## 3) Modelos entrenados y rendimiento

###3.1 Regresión Logística (mejor modelo)

**Seleccionada como mejor:** best_model = lr_best_model con solver='newton-cg' y C ≈ 46.42 (semilla 42).

**Métricas en test:**

**precision_score:** 0.735

**recall_score:** 0.805

**f1_score:** 0.768

**roc_auc_score:** 0.836

**Interpretabilidad:** Los coeficientes confirman el peso de Contract, tenure, y la disponibilidad de OnlineSecurity/TechSupport. (La gráfica de FeatureImportances(best_model) refleja estos pesos.)

### **3.2 Random Forest**

**Búsqueda aleatoria (RandomizedSearchCV) y mejor estimador mostrado como:**

RandomForestClassifier(n_estimators=750, max_depth=8, max_features=None, random_state=42, ...)
(parámetros intermedios como min_samples_split aparecen acotados en el notebook).

Rendimiento similar en precisión/recall (las impresiones de métricas rondan 0.735–0.738 de precision y 0.802–0.805 de recall).

### **3.3 Árbol de Decisión**

**Mejor modelo mostrado como:**
DecisionTreeClassifier(class_weight='balanced', max_depth=5, min_samples_leaf=5, random_state=42).

Útil para interpretabilidad, pero con rendimiento inferior al conjunto.

**Conclusión de performance:** La Regresión Logística logra el mejor equilibrio (AUC ≈ 0.836) y ofrece interpretabilidad directa para accionar estrategias.

## **4) Principales factores que afectan la cancelación (dirección del efecto)**

Basado en las variables seleccionadas y en la importancia/coeficientes del mejor modelo.

###**Contract**

Mes a mes: ↑ Churn (alto riesgo).

Two year: ↓ Churn (sólida retención).

Tenure (antigüedad)

Baja antigüedad: ↑ Churn.

Antigüedad alta: ↓ Churn (efecto protector).

OnlineSecurity / TechSupport

No contratado: ↑ Churn.

Contratado: ↓ Churn (aporta valor percibido y reduce fricción).

### **InternetService**

Segmentos como Fiber optic tienden a ↑ Churn si el precio/soporte no acompañan (suele requerir empaquetado de valor).

PaymentMethod

Electronic check: ↑ Churn típico.

Pagos automáticos (tarjeta/cuenta): ↓ Churn.

### **PaperlessBilling**

Aislado, su efecto puede variar; en combinación con electronic check se asocia con mayor riesgo.

## **SeniorCitizen**

Señal de ↑ Churn si no hay acompañamiento (soporte y planes flexibles).

## **Total (gasto)**

**Ticket alto sin beneficios percibidos:** ↑ Churn; si el paquete incluye valor (Soporte/Security, descuentos), el riesgo baja.

## **5) Estrategias de retención basadas en los hallazgos**

### **5.1 Contrato y pricing**

**Migración a plazos más largo:** campañas de upgrade de Month-to-Month → 12/24 meses con:

Descuento escalonado los primeros 3–6 meses.

Bundle con OnlineSecurity y/o TechSupport sin costo inicial.

Evitar shocks de precio: para clientes con Total alto y bajo uso, ofrecer:

Repaquetización (menos costo, más valor percibido).

Créditos o price protection por 6–12 meses.

### **5.2 Valor agregado (servicios)**

Bundling proactivo de OnlineSecurity y TechSupport a clientes de Fiber optic o recién adquiridos (tenure bajo).

Soporte priorizado para SeniorCitizen: línea dedicada, tutoriales simples, visitas/llamadas de onboarding.

### **5.3 Pagos y facturación**

Incentivar pagos automáticos (tarjeta/cuenta) con bono mensual pequeño.

Revisar Electronic check: migrarlos a AutoPay con beneficios (p. ej., 1–2% de descuento o GB extra).

### **5.4 Experiencia en los primeros 90 días (tenure bajo)**

Programa “Golden 90”:

Welcome call + chequeo de calidad de servicio (velocidad/latencia).

Push de features útiles (app, autogestión, diagnóstico).

Encuesta NPS temprana → ticket de soporte proactivo si hay disconformidad.

### **5.5 Retención basada en riesgo (uso del modelo)**

Scoring mensual con el modelo (umbral calibrado para recall alto ~0.80).

Playbooks por motivo:

Precio: oferta de plan optimizado.

Soporte: fast-track con SLA corto.

Servicio (InternetService): revisión técnica + mejora de router/ONT si aplica.

## **6) Prioridad de segmentos (impacto esperado)**

Mes a mes + tenure < 6 meses + sin OnlineSecurity/TechSupport → Muy alto impacto si migran a plan anual con bundle.

Fiber optic + Electronic check + PaperlessBilling → Alto impacto al mover a AutoPay y añadir TechSupport.

SeniorCitizen con incidencias recientes → Alto impacto con soporte preferente y descuento de fidelidad.

Ticket alto con bajo uso → Medio/Alto al reempaquetar valor.

## **7) Métricas de éxito y monitoreo**

Churn mensual y retención a 90/180 días por segmento.

Tasa de adopción de contratos 12/24 meses y AutoPay.

NPS y tiempo a resolución en cohortes de alto riesgo.

Lift por campaña: comparar tasa de cancelación de tratados vs. control.

## **8) Próximos pasos técnicos**

Calibrar probabilidades (Platt/Isotónica) y revisar el umbral vs. costo de error.

Monitoreo de deriva (datos y performance) trimestral.

Explicabilidad: guardar coeficientes y SHAP (complemento) para auditoría interna.

Experimentos A/B de ofertas (duración vs. bundle) en los segmentos priorizados.

### **9) Resumen técnico del pipeline**

Split train_test_split, CV=5.

Desbalance: downsampling aplicado.

Prepro: estandarización numéricas + OneHotEncoder en categóricas (InternetService, Contract, PaymentMethod).

Modelos:

LogisticRegression (mejor): solver='newton-cg', C≈46.42, random_state=0.

RandomForest (mejor hallado): n_estimators=750, max_depth=8, max_features=None, random_state=42 (otros hiperparámetros en notebook).

DecisionTree: class_weight='balanced', max_depth=5, min_samples_leaf=5, random_state=42.
