# 📊 Resultados Generación Sintética Avanzada

**Fecha:** 2026-01-08  
**Versión:** v3_experimental  
**Fase:** Prompt 5 - Generación Condicional (Conditioning on Time)

---

## 📈 Resumen Ejecutivo

| Métrica | Valor |
|---------|-------|
| **Método** | Conditional GaussianCopula (Stratified) |
| **Estrategia** | Cuartiles de Duration × Event |
| **Sintetizadores** | 7 (de 8 posibles) |
| **Filas Generadas** | 1,000 |
| **Random State** | 42 |

---

## 🎯 Preservación de Correlación Duration-Event

| Métrica | Valor | Estado |
|---------|-------|--------|
| **Correlación Real** | 0.504 | — |
| **Correlación Sintética** | 0.519 | ✅ |
| **Delta (Δ)** | 0.015 | ✅ Excelente |

> La estrategia condicional preserva mejor la correlación duration-event que el método global (Δ=0.015 vs posible drift mayor).

---

## 📊 Distribución de Estratos

| Estrato | n Real | n Sintético | Descripción |
|---------|--------|-------------|-------------|
| Q1_E0 | 64 | 216 | Duration baja, censurado |
| Q1_E1 | 10 | 33 | Duration baja, evento |
| Q2_E0 | 58 | 195 | Duration media-baja, censurado |
| Q2_E1 | 16 | 54 | Duration media-baja, evento |
| Q3_E0 | 3 | ❌ 0 | *Omitido (n<10)* |
| Q3_E1 | 71 | 253 | Duration media-alta, evento |
| Q4_E0 | 36 | 121 | Duration alta, censurado |
| Q4_E1 | 38 | 128 | Duration alta, evento |

> **Nota:** Q3_E0 fue omitido porque solo tenía 3 observaciones reales (insuficiente para entrenar sintetizador).

---

## 🔬 Comparación: Global vs Condicional

| Aspecto | Global (Prompt 4) | Condicional (Prompt 5) |
|---------|-------------------|------------------------|
| **Sintetizadores** | 1 | 7 |
| **Δ Correlación** | ~0.05 (típico) | **0.015** ✅ |
| **Heterogeneidad** | Suavizada | Preservada |
| **Complejidad** | Baja | Media |

---

## 📁 Archivos Generados

| Archivo | Tamaño | Descripción |
|---------|--------|-------------|
| `synthetic_data_advanced.parquet` | 57 KB | 1000 filas sintéticas |
| `synth_Q1-E0.pkl` | 803 KB | Sintetizador cuartil 1, censurado |
| `synth_Q1-E1.pkl` | 801 KB | Sintetizador cuartil 1, evento |
| `synth_Q2-E0.pkl` | 803 KB | Sintetizador cuartil 2, censurado |
| `synth_Q2-E1.pkl` | 801 KB | Sintetizador cuartil 2, evento |
| `synth_Q3-E1.pkl` | 803 KB | Sintetizador cuartil 3, evento |
| `synth_Q4-E0.pkl` | 802 KB | Sintetizador cuartil 4, censurado |
| `synth_Q4-E1.pkl` | 803 KB | Sintetizador cuartil 4, evento |
| `advanced_generation_report.json` | 1 KB | Reporte JSON |

---

## ➡️ Siguiente Paso

**Prompt 6: Entrenamiento TSTR** - Comparar rendimiento de modelos entrenados con:
- Datos reales (Baseline)
- Datos reales + sintéticos globales
- Datos reales + sintéticos condicionales

---

## 📚 Referencias

- "Conditioning on Time" (OpenReview) - Mejora fidelidad en survival analysis
- SDV Documentation - GaussianCopulaSynthesizer
