# 📊 Resumen Ejecutivo: Experimento de Datos Sintéticos

**Fecha:** 2026-01-08 | **Versión:** v3_experimental

---

## 🎯 Objetivo
Evaluar si la augmentación con datos sintéticos mejora modelos de supervivencia.

## 📈 Resultados Clave

| Modelo | Baseline | + Synthetic | Δ C-index | Veredicto |
|--------|----------|-------------|-----------|-----------|
| **RSF** | 0.474 | 0.477 | **+0.3%** | ✅ Mejora |
| **XGBoost** | 0.482 | 0.470 | -1.2% | ❌ Degrada |

## 🔒 Privacidad
- **Copias exactas:** 0/2000 ✅
- **DCR mínimo:** 1.33 (seguro)

## 📁 Estructura Final

```
v3_experimental/
├── 01_diagnosis/     ✅ Dataset diagnosis
├── 02_protocol/      ✅ TSTR protocol
├── 03_baseline/      ✅ Baseline metrics
├── 04_synthetic_sdv/ ✅ Global synthesis (1000 rows)
├── 05_synthetic_adv/ ✅ Conditional synthesis
├── 07_privacy_check/ ✅ DCR audit passed
├── 08_eval_rsf/      ✅ RSF evaluation
├── 09_eval_xgb/      ✅ XGBoost evaluation
└── 10_final_report/  ✅ Thesis chapter
```

## ✅ Conclusión

> **RSF + Synthetic Advanced:** Recomendado (+0.3%)
> **XGBoost:** Mantener baseline (sensible a ruido sintético)
