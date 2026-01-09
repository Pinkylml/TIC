# 📊 Resultados Evaluación RSF TSTR

**Fecha:** 2026-01-08  
**Versión:** v3_experimental  
**Fase:** Prompt 8 - Evaluación Downstream RSF

---

## 🏆 Resultado Principal

| Escenario | n Train | C-index | Δ vs Baseline | Estado |
|-----------|---------|---------|---------------|--------|
| **Baseline** | 296 | 0.4741 | — | — |
| Copula (100%) | 592 | 0.4730 | -0.001 | ⚠️ Similar |
| **Advanced (100%)** | 592 | **0.4774** | **+0.003** | ✅ |

> 🏆 **Mejor Modelo:** Real + Advanced (100%) con C-index = **0.4774**

---

## 📈 Conclusiones

| Pregunta | Respuesta |
|----------|-----------|
| ¿Los datos sintéticos ayudan? | ✅ Sí (Advanced) |
| ¿Cuál método sintético es mejor? | **Advanced (Condicional)** |
| ¿La mejora es significativa? | ⚠️ Marginal (+0.3%) |

---

## 🔬 Análisis

### Método Copula (Global)
- **Δ = -0.001** → No mejora, ligeramente peor
- Posible causa: El sintetizador global suaviza la heterogeneidad temporal

### Método Advanced (Condicional)
- **Δ = +0.003** → Mejora marginalmente
- Ventaja: Preserva mejor las correlaciones condicionales por cuartil/evento

---

## 📁 Archivos Generados

| Archivo | Tamaño | Descripción |
|---------|--------|-------------|
| `rsf_results.csv` | 314 B | Tabla de resultados |
| `rsf_evaluation_report.json` | 1 KB | Reporte JSON |
| `rsf_comparison.png` | 64 KB | Gráfico de barras |
| `rsf_baseline.pkl` | 9.5 MB | Modelo baseline |
| `rsf_copula.pkl` | 20.8 MB | Modelo copula |
| `rsf_advanced.pkl` | 21.4 MB | Modelo advanced |

---

## ➡️ Siguiente Paso

**Prompt 9**: Evaluación TSTR para XGBoost-AFT para confirmar si los resultados son consistentes entre modelos.

---

## 📚 Nota Metodológica

- Test set: n=75, event rate=45.3%
- Random state: 42
- Hiperparámetros RSF: n_estimators=100, max_depth=5
