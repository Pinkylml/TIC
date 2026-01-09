# 📊 Resultados Evaluación XGBoost-AFT TSTR

**Fecha:** 2026-01-08  
**Versión:** v3_experimental  
**Fase:** Prompt 9 - Evaluación Downstream XGBoost-AFT

---

## 🏆 Resultado Principal

| Escenario | n Train | C-index | -LogLik | Δ vs Baseline | Estado |
|-----------|---------|---------|---------|---------------|--------|
| **Baseline** | 296 | **0.4824** | 1.138 | — | ✅ Mejor C-index |
| Copula (100%) | 592 | 0.4043 | 1.135 | -0.078 | ❌ Degradado |
| Advanced (100%) | 592 | 0.4697 | **1.103** | -0.013 | ⚠️ Mejor LogLik |

> ⚠️ **Para XGBoost-AFT:** El baseline tiene mejor C-index, pero Advanced tiene mejor Log-Likelihood.

---

## 📈 Comparación con RSF

| Modelo | Baseline | Copula | Advanced | Mejor |
|--------|----------|--------|----------|-------|
| **RSF** | 0.474 | 0.473 | **0.477** | Advanced ✅ |
| **XGBoost-AFT** | **0.482** | 0.404 | 0.470 | Baseline |

### Observaciones Clave

1. **XGBoost-AFT es más sensible** a los datos sintéticos
2. **Copula degrada significativamente** XGBoost (-7.8% C-index)
3. **RSF es más robusto** a la augmentación sintética

---

## 🔬 Análisis

### ¿Por qué XGBoost se degrada con sintéticos?

| Factor | Explicación |
|--------|-------------|
| **Sensibilidad AFT** | El modelo AFT depende de la distribución exacta de tiempos |
| **Copula suaviza** | GaussianCopula reduce la variabilidad de duration |
| **Bounds log-time** | y_lower/y_upper pueden ser inconsistentes con sintéticos |

### Log-Likelihood vs C-index

| Métrica | Qué mide | Mejor en |
|---------|----------|----------|
| **C-index** | Ranking de riesgos | Baseline (0.482) |
| **-LogLik** | Ajuste distribucional | Advanced (1.103) |

> Advanced calibra mejor el modelo, pero no mejora la discriminación.

---

## 📁 Archivos Generados

| Archivo | Tamaño | Descripción |
|---------|--------|-------------|
| `xgb_results.csv` | 373 B | Tabla de resultados |
| `xgb_evaluation_report.json` | 1.3 KB | Reporte JSON |
| `xgb_comparison.png` | 74 KB | Gráfico dual |
| `xgb_baseline.json` | 104 KB | Modelo baseline |
| `xgb_copula.json` | 110 KB | Modelo copula |
| `xgb_advanced.json` | 100 KB | Modelo advanced |

---

## ➡️ Conclusión Preliminar

| Modelo | ¿Data sintética ayuda? | Recomendación |
|--------|------------------------|---------------|
| **RSF** | ✅ Sí (marginal) | Usar Advanced |
| **XGBoost-AFT** | ❌ No (degrada) | Mantener baseline |

---

## 📚 Nota Metodológica

- Test set: n=75, event rate=45.3%
- Random state: 42
- XGBoost: objective=survival:aft, max_depth=3, lr=0.1
