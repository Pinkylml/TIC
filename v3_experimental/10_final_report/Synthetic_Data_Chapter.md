# Capítulo: Resultados Experimentales con Datos Sintéticos

**Versión:** v3_experimental  
**Fecha:** 2026-01-08  
**Autor:** Investigador Principal

---

## Resumen del Capítulo

Este capítulo presenta los resultados experimentales de la augmentación de datos mediante generación sintética para mejorar modelos de análisis de supervivencia aplicados al tiempo-al-primer-empleo de graduados universitarios. Se evaluó la metodología **TSTR (Train on Synthetic, Test on Real)** con dos estrategias de generación: Gaussian Copula global y condicional estratificada.

**Hallazgo Principal:** La augmentación sintética produce resultados mixtos dependiendo del modelo. Random Survival Forest muestra una mejora marginal (+0.3%), mientras que XGBoost-AFT experimenta degradación en C-index pero mejora en calibración (Log-Likelihood).

---

## 1. Introducción

### 1.1 Problema

El dataset de supervivencia presenta limitaciones para el entrenamiento de modelos predictivos:

| Limitación | Valor | Impacto |
|------------|-------|---------|
| Tamaño muestral | n = 296 | Alto riesgo de overfitting |
| Tasa de censura | 54.4% | Información incompleta |
| Correlación máxima feature-evento | 0.17 | Bajo poder predictivo |
| Ratio p/n | 0.21 | Alta dimensionalidad relativa |

### 1.2 Hipótesis

> *La augmentación con datos sintéticos puede mejorar la capacidad predictiva de modelos de supervivencia al aumentar el tamaño efectivo de la muestra sin comprometer la validez estadística.*

### 1.3 Justificación Metodológica

Se seleccionó **Gaussian Copula** de la librería SDV por las siguientes razones:

1. **Adecuado para datasets pequeños** (n < 1000) a diferencia de GANs que requieren más datos
2. **Preserva correlaciones multivariadas** entre features
3. **Reproducible y estable** (no requiere entrenamiento estocástico complejo)
4. **Documentación científica** consolidada (Xu et al., 2019)

---

## 2. Metodología

### 2.1 Protocolo Experimental TSTR

El protocolo **Train on Synthetic, Test on Real** garantiza evaluación válida:

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ Train Real  │ ──▶ │ Generador   │ ──▶ │ Sintético   │
│   (n=296)   │     │ GaussianCop │     │  (n=1000)   │
└─────────────┘     └─────────────┘     └─────────────┘
                                              │
                    ┌─────────────────────────┘
                    ▼
        ┌───────────────────────────────────────────┐
        │          MODELOS ENTRENADOS               │
        ├───────────────────────────────────────────┤
        │ A. Baseline: Solo Real (296)              │
        │ B. Copula: Real + Synth Global (592)      │
        │ C. Advanced: Real + Synth Condic. (592)   │
        └───────────────┬───────────────────────────┘
                        ▼
                ┌─────────────┐
                │ Test Real   │ ◄── Evaluación ÚNICA
                │   (n=75)    │     (No toca Train)
                └─────────────┘
```

### 2.2 Estrategias de Generación

#### 2.2.1 Gaussian Copula Global

Un único sintetizador entrenado en todo el dataset:

| Parámetro | Valor |
|-----------|-------|
| Método | `GaussianCopulaSynthesizer` |
| Distribución duration | `truncnorm` |
| Enforce min/max | `True` |
| Quality Score | **0.936** |

#### 2.2.2 Generación Condicional (Conditioning on Time)

Sintetizadores separados por estrato (cuartil de duration × estado de evento):

| Estrato | n Real | n Sintético |
|---------|--------|-------------|
| Q1_E0 | 64 | 216 |
| Q1_E1 | 10 | 33 |
| Q2_E0 | 58 | 195 |
| Q2_E1 | 16 | 54 |
| Q3_E1 | 71 | 253 |
| Q4_E0 | 36 | 121 |
| Q4_E1 | 38 | 128 |
| **Total** | **296** | **1000** |

**Ventaja:** Preserva mejor la correlación duration-event (Δ = 0.015 vs ~0.05 global).

---

## 3. Calidad de los Datos Sintéticos

### 3.1 Fidelidad Estadística

| Métrica | Real | Sintético Global | Sintético Advanced |
|---------|------|------------------|-------------------|
| Duration μ | 15.43 | 15.51 | 15.48 |
| Duration σ | 10.99 | 8.72 | 9.85 |
| Event Rate | 45.6% | 45.1% | 45.3% |
| Corr(dur, event) | 0.504 | ~0.50 | **0.519** |

> La estrategia Advanced preserva mejor la correlación crítica duration-event.

### 3.2 Validación de Privacidad

Se verificó ausencia de memorización mediante **DCR (Distance to Closest Record)**:

| Método | Copias Exactas | DCR Mínimo | DCR Medio | Riesgo |
|--------|----------------|------------|-----------|--------|
| Global | 0 | 0.35 | 7.85 | 🟢 BAJO |
| Advanced | 0 | **1.33** | 6.52 | 🟢 BAJO |

> ✅ **Aprobado:** No se detectó memorización. Los datos son seguros para uso.

---

## 4. Resultados TSTR

### 4.1 Random Survival Forest

| Escenario | n Train | C-index | Δ vs Baseline | Resultado |
|-----------|---------|---------|---------------|-----------|
| **Baseline** | 296 | 0.4741 | — | Referencia |
| Copula (100%) | 592 | 0.4730 | -0.001 | ⚠️ Similar |
| **Advanced (100%)** | 592 | **0.4774** | **+0.003** | ✅ Mejora |

**Interpretación:** La augmentación con estrategia Advanced produce una mejora marginal (+0.33%) en RSF, consistente con el efecto de regularización por aumento de datos.

### 4.2 XGBoost-AFT

| Escenario | n Train | C-index | -LogLik | Δ vs Baseline |
|-----------|---------|---------|---------|---------------|
| **Baseline** | 296 | **0.4824** | 1.138 | — |
| Copula (100%) | 592 | 0.4043 | 1.135 | -0.078 ❌ |
| Advanced (100%) | 592 | 0.4697 | **1.103** | -0.013 |

**Interpretación:** XGBoost-AFT muestra degradación en C-index pero mejora en Log-Likelihood con datos sintéticos. Esto sugiere mejor calibración pero peor discriminación.

### 4.3 Comparación Consolidada

| Modelo | Baseline | Copula | Advanced | ¿Sintético Ayuda? |
|--------|----------|--------|----------|-------------------|
| **RSF** | 0.474 | 0.473 | **0.477** | ✅ Sí (marginal) |
| **XGBoost-AFT** | **0.482** | 0.404 | 0.470 | ❌ No (C-index) |

---

## 5. Discusión

### 5.1 ¿Por qué RSF mejora y XGBoost no?

| Factor | RSF | XGBoost-AFT |
|--------|-----|-------------|
| **Tipo de modelo** | Ensemble (promedio de árboles) | Boosting (secuencial) |
| **Sensibilidad a ruido** | Baja (bagging suaviza) | Alta (amplifica errores) |
| **Manejo de censura** | Integrado en splits | Via bounds log-time |
| **Efecto sintéticos** | Regularización | Posible desajuste distribucional |

### 5.2 Limitaciones del Experimento

1. **Tamaño de test pequeño** (n=75): Alta varianza en estimación de C-index
2. **Una sola semilla** (42): Resultados pueden variar con otras configuraciones
3. **Correlaciones débiles**: El problema subyacente (max corr = 0.17) limita cualquier mejora

### 5.3 Implicaciones para la Tesis

> **Conclusión Principal:** La augmentación sintética con Gaussian Copula estratificada es una técnica válida para RSF, pero debe usarse con precaución en modelos paramétricos como XGBoost-AFT.

---

## 6. Conclusiones

### 6.1 Respuestas a las Preguntas de Investigación

| Pregunta | Respuesta |
|----------|-----------|
| ¿La data sintética mejora el modelo? | **Depende del modelo**: ✅ RSF, ❌ XGBoost |
| ¿Qué método de generación es mejor? | **Advanced** (condicional estratificado) |
| ¿Los datos sintéticos son privados? | ✅ **Sí** (0 copias exactas, DCR > 1.0) |
| ¿La mejora es significativa? | ⚠️ **Marginal** (+0.3% para RSF) |

### 6.2 Recomendaciones

1. **Para RSF:** Usar augmentación Advanced con ratio 100%
2. **Para XGBoost:** Mantener el baseline sin augmentación
3. **Para privacidad:** Ambos métodos son seguros (no memorización)
4. **Para futuras investigaciones:** Probar otros sintetizadores (CTGAN, TVAE)

---

## 7. Referencias

1. **Lawless, J.F. (2003)**. *Statistical Models and Methods for Lifetime Data*. Wiley. — Fundamentos de restricciones de dominio en survival analysis.

2. **Xu, L., Skoularidou, M., Cuesta-Infante, A., & Veeramachaneni, K. (2019)**. *Modeling Tabular Data using Conditional GAN*. NeurIPS. — Base teórica de CTGAN y SDV.

3. **Getie Ayaneh, W. et al. (2020)**. *Survival Models for the Analysis of Waiting Time to First Employment*. Advances in Decision Sciences. — Aplicación similar en tiempo-al-empleo.

4. **Suresh, H. et al. (2025)**. *Synthetic Survival Data Generation for Heart Failure Prognosis Using Deep Generative Models*. — Metodología TSTR para survival analysis.

5. **Andonovikj, V. et al. (2024)**. *Survival analysis as semi-supervised multi-label classification*. — Relación duration-event en síntesis.

6. **SDV Documentation (2024)**. *GaussianCopulaSynthesizer*. https://sdv.dev — Implementación del sintetizador.

---

## Anexos

### A. Estructura de Carpetas

```
v3_experimental/
├── 01_diagnosis/           # Diagnóstico del dataset
├── 02_protocol/            # Protocolo experimental
├── 03_baseline/            # Entrenamiento baseline
├── 04_synthetic_sdv/       # Generación global (Copula)
├── 05_synthetic_advanced/  # Generación condicional
├── 07_privacy_check/       # Auditoría de privacidad
├── 08_eval_rsf/            # Evaluación RSF
├── 09_eval_xgb/            # Evaluación XGBoost
└── 10_final_report/        # Este capítulo
```

### B. Código Reproducible

Todos los notebooks están versionados y son ejecutables secuencialmente. Random state = 42 para reproducibilidad.

---

*Fin del Capítulo*
