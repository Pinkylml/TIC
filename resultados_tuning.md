# 🏆 Resultados del Hyperparameter Tuning

**Fecha:** 2026-01-08  
**Dataset:** `train_survival_v2.parquet` (296 registros, 61 features)

---

## 🌲 Random Survival Forest (RSF)

| Métrica | Valor |
|---------|-------|
| **C-index promedio (CV)** | **0.5669** |
| n_estimators | 500 |
| min_samples_leaf | 20 |
| max_depth | None (sin límite) |
| max_features | sqrt |

**Interpretación del C-index:**
- 0.5 = predicción aleatoria
- 0.7+ = predicción aceptable
- 0.8+ = predicción buena

> ⚠️ C-index = 0.5669 indica capacidad predictiva limitada. Posibles causas:
> - Dataset pequeño (N=296)
> - Ventana de observación corta (0.5-6 meses)
> - Alta censura (54.4%)

---

## 🚀 XGBoost-AFT (Accelerated Failure Time)

| Métrica | Valor |
|---------|-------|
| **Loss (aft-nloglik)** | **15.7328** |
| **Distribución ganadora** | **normal** |
| Mejores rondas (num_boost_round) | 2 |
| learning_rate | 0.05 |
| max_depth | 5 |
| min_child_weight | 3 |
| reg_lambda | 1.0 |

**Interpretación de la distribución:**
- `normal` ganó sobre `logistic`
- Esto sugiere que el **tiempo de búsqueda de empleo** sigue una distribución simétrica (normal)
- Los tiempos de espera no tienen colas pesadas

> ⚠️ Solo 2 rondas de boosting indica que el modelo no encontró patrones complejos en los datos.

---

## 📁 Modelos Guardados

```
models/
├── best_rsf.pkl           # Random Survival Forest (679 KB)
├── best_rsf_params.pkl    # Parámetros RSF
├── best_xgb_aft.json      # XGBoost-AFT (6 KB)
└── best_xgb_params.pkl    # Parámetros XGBoost
```

---

## 📊 Comparación de Modelos

| Modelo | Métrica | Valor | Complejidad |
|--------|---------|-------|-------------|
| RSF | C-index | 0.5669 | Alta (500 árboles) |
| XGBoost-AFT | nloglik | 15.73 | Baja (2 rondas) |

---

## 🔍 Recomendaciones

1. **Aumentar datos**: El dataset es muy pequeño para survival analysis robusto
2. **Extender ventana**: 6 meses de observación puede ser insuficiente
3. **Feature engineering**: Considerar interacciones carrera × habilidades
4. **Validación externa**: Probar en cohortes de otros años
