# 📊 Resultados Baseline Training

**Fecha:** 2026-01-08  
**Versión:** v3_experimental  
**Fase:** Prompt 3 - Baseline (Escenario A: Real Only)

---

## 📈 Resumen Ejecutivo

| Métrica | RSF | XGBoost-AFT | 🏆 Mejor |
|---------|-----|-------------|----------|
| **C-index (Train)** | 0.7210 | 0.7544 | XGB |
| **C-index (Test)** | 0.4675 | **0.4791** | **XGB** |
| **IBS** | 0.1204 | N/A | RSF |

> **Mejor modelo:** XGBoost-AFT con C-index = **0.479** en test set

---

## ⚠️ Diagnóstico

### Overfitting Detectado

| Modelo | Gap (Train - Test) | Severidad |
|--------|-------------------|-----------|
| RSF | 0.254 | 🔴 Alto |
| XGBoost-AFT | 0.275 | 🔴 Alto |

### Causa Raíz (del diagnóstico previo)

- **Correlación máxima feature-event:** 0.17 (muy baja)
- **Dataset pequeño:** n=296
- **Alta dimensionalidad relativa:** p/n = 0.21

> El overfitting es esperado dado que ninguna feature tiene poder predictivo fuerte.

---

## 📊 Detalles del Dataset

| Parámetro | Train | Test |
|-----------|-------|------|
| n | 296 | 75 |
| Features | 59 | 59 |
| Event Rate | 45.6% | 45.3% |

---

## 🎯 Interpretación del C-index

| Rango | Interpretación | Estado Actual |
|-------|----------------|---------------|
| 0.50 | Aleatorio | — |
| 0.50 - 0.60 | Pobre | ⚠️ **Estamos aquí** |
| 0.60 - 0.70 | Aceptable | — |
| 0.70 - 0.80 | Bueno | — |
| > 0.80 | Excelente | — |

> El C-index de ~0.48 está apenas por debajo del umbral aleatorio, confirmando la dificultad predictiva del problema.

---

## 📁 Archivos Generados

| Archivo | Tamaño | Descripción |
|---------|--------|-------------|
| `03_Baseline_Training.ipynb` | 21 KB | Notebook original |
| `03_Baseline_Training_executed.ipynb` | 21 KB | Notebook ejecutado |
| `baseline_metrics.json` | 1 KB | Métricas JSON |
| `rsf_baseline.pkl` | 9 MB | Modelo RSF |
| `xgb_aft_baseline.json` | 103 KB | Modelo XGBoost-AFT |

---

## 🔢 Línea Base para Comparación

```json
{
  "baseline_c_index": 0.479,
  "success_threshold": 0.459,  // baseline - 0.02
  "model": "XGBoost-AFT"
}
```

> Los escenarios sintéticos (B, C, D) deben lograr C-index ≥ **0.459** para considerarse exitosos.

---

## ➡️ Siguiente Paso

**Prompt 4: Generación Sintética** - Crear datos sintéticos con CTGAN/GaussianCopula para augmentar el entrenamiento.

---

## 📚 Notas Metodológicas

### Hiperparámetros Usados

**RSF:**
- n_estimators: 100
- max_depth: 5
- min_samples_split: 10
- min_samples_leaf: 5

**XGBoost-AFT:**
- objective: survival:aft
- aft_loss_distribution: normal
- max_depth: 3
- learning_rate: 0.1
- num_boost_round: 100

### Semilla Aleatoria
- Random State: **42**
