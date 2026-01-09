# 📊 Resultados Generación Sintética SDV

**Fecha:** 2026-01-08  
**Versión:** v3_experimental  
**Fase:** Prompt 4 - Generación Sintética (GaussianCopula)

---

## 📈 Resumen Ejecutivo

| Métrica | Valor |
|---------|-------|
| **Método** | GaussianCopulaSynthesizer |
| **Filas Generadas** | 1,000 |
| **Quality Score SDV** | **0.936** ✅ |
| **Random State** | 42 |

---

## 🔍 Comparación Estadística

### Variables Clave

| Variable | Real (μ) | Sintético (μ) | Δ | Estado |
|----------|----------|---------------|---|--------|
| **duration** | 15.43 | 15.51 | 0.08 | ✅ Excelente |
| **event_rate** | 45.6% | 45.1% | 0.5% | ✅ Excelente |

### Desviación Estándar

| Variable | Real (σ) | Sintético (σ) | Nota |
|----------|----------|---------------|------|
| **duration** | 10.99 | 8.72 | ⚠️ Menor variabilidad |

> La menor variabilidad en `duration` es típica de modelos Copula; las colas de la distribución se suavizan.

---

## 🔒 Restricciones de Dominio (Post-Procesamiento)

| Restricción | Aplicada | Resultado |
|-------------|----------|-----------|
| `duration > 0` | ✅ | Clip a min observado (0.58) |
| `event ∈ {0, 1}` | ✅ | Round + clip |
| `edad ∈ [21, 40]` | ✅ | Clip a rango |
| `tech_* ∈ {0, 1}` | ✅ | 48 columnas binarias |
| `hab_* ∈ [0, 0.25, 0.5, 0.75, 1]` | ✅ | 7 columnas discretizadas |

---

## ⚠️ Columnas Excluidas

```
❌ tech_python    (zero-variance, solo 0)
❌ tech_big_data  (zero-variance, solo 0)
```

---

## 📁 Archivos Generados

| Archivo | Tamaño | Descripción |
|---------|--------|-------------|
| `synthetic_data_copula.parquet` | 59 KB | 1000 filas sintéticas |
| `synthesizer_model.pkl` | 804 KB | Modelo GaussianCopula |
| `synthesizer_metadata.json` | 5 KB | Metadatos SDV |
| `generation_report.json` | 1 KB | Reporte de generación |
| `04_Synthetic_Generation.ipynb` | 22 KB | Notebook original |
| `04_Synthetic_Generation_executed.ipynb` | 38 KB | Notebook ejecutado |

---

## 🎯 Calidad para TSTR

| Criterio | Esperado | Obtenido | Estado |
|----------|----------|----------|--------|
| Quality Score | ≥ 0.80 | 0.936 | ✅ |
| Event Rate Δ | ≤ 5% | 0.5% | ✅ |
| Duration Mean Δ | ≤ 2 | 0.08 | ✅ |

> Los datos sintéticos son de **alta calidad** y listos para el entrenamiento TSTR.

---

## ➡️ Siguiente Paso

**Prompt 5: Entrenamiento TSTR** - Entrenar modelos con:
- Escenario A: Solo Real (296) ✅ Ya completado
- Escenario B: Real + 1x Synth (296 + 296 = 592)
- Escenario C: Real + 2x Synth (296 + 592 = 888)
- Escenario D: Solo Synth (296)

---

## 📚 Referencias

- SDV Documentation: GaussianCopulaSynthesizer
- Xu et al. (2019): Modeling Tabular Data using Conditional GAN
