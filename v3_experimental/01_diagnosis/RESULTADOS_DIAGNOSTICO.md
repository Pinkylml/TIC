# 📊 Resultados del Diagnóstico de Datos

**Fecha:** 2026-01-08  
**Versión:** 2.0 (Enhanced)  
**Fuente:** `train_final.parquet` (Encuesta recién graduados - EPN)

---

## 📈 Resumen Ejecutivo

| Métrica | Valor | Estado |
|---------|-------|--------|
| Filas | 296 | ⚠️ Pequeño |
| Columnas | 63 | - |
| Tasa de Censura | 54.4% | ✅ Rango esperado |
| Ratio p/N | 0.21 | ⚠️ Alto |

---

## 📋 Clasificación de Variables

| Tipo | Cantidad | Ejemplos |
|------|----------|----------|
| **Continuas** | 2 | `edad`, `duration` |
| **Binarias** | 50 | `genero_m`, `tech_*` |
| **Categóricas** | 9 | `hab_1` a `hab_7` |
| **Zero-variance** | 2 | `tech_python`, `tech_big_data` |

---

## 🎯 Análisis del Target

```
Eventos (E=1):     135 (45.6%)  → Encontraron empleo STEM
Censurados (E=0):  161 (54.4%)  → Aún buscando

Duration range:    [0.58, 30.00] meses
Duration mean:     15.43 ± 10.99 meses
```

---

## 📈 Análisis de Correlaciones

| Relación | Correlación | Interpretación |
|----------|-------------|----------------|
| Duration ↔ Event | **0.504** | Moderada-alta (preservar) |
| Features → Event | **max 0.17** | 🚨 CRÍTICO: muy baja |

> **Implicación:** Ninguna feature individual tiene poder predictivo fuerte sobre el evento de empleo. Esto explica el C-index bajo (0.5669) de los modelos.

---

## 🔒 Restricciones de Dominio

### Hard Constraints (NO violar)
- `duration > 0` - Lawless (2003)
- `event ∈ {0, 1}`
- `tech_* ∈ {0, 1}`

### Soft Constraints (Mantener)
- `edad ∈ [21, 40]`
- `hab_* ∈ [0, 1]`
- `duration ∈ [0.58, 30]`

---

## ⚠️ Variables Problemáticas

### Zero-Variance (EXCLUIR del sintetizador)
```
❌ tech_python    → solo valor = 0
❌ tech_big_data  → solo valor = 0
```

### Sparse (<5% valores = 1)
29 features técnicas tienen muy pocos "1", incluyendo:
- `tech_java`, `tech_machine_learning`, `tech_robotica`
- `tech_geologia`, `tech_petroleos`, `tech_agroindustria`

---

## 🎮 Reglas para Generación Sintética

```python
# Configuración recomendada para sintetizador
synthesis_config = {
    "method": "GaussianCopula",  # Mejor para N < 500
    "exclude_features": ["tech_python", "tech_big_data"],
    
    "preservation_targets": {
        "censoring_rate": 0.544,    # ±5%
        "duration_event_corr": 0.50  # ±0.1
    },
    
    "post_processing": {
        "duration": "clip_to_positive",
        "event": "round_to_binary",
        "tech_*": "round_to_binary"
    }
}
```

---

## 🚨 Problemas Identificados

1. **CRÍTICO:** Ninguna feature tiene correlación > 0.2 con event
2. **Warning:** Dataset pequeño (N=296 < 500 recomendado)
3. **Warning:** Alta dimensionalidad relativa (p/N = 0.21)
4. **Warning:** 2 features con varianza cero a excluir

---

## 📚 Referencias Científicas

| Autor | Año | Relevancia |
|-------|-----|------------|
| Lawless, J.F. | 2003 | Fundamentos survival analysis |
| Getie Ayaneh et al. | 2020 | Tiempo-al-empleo en graduados |
| Andonovikj et al. | 2024 | Síntesis de datos de supervivencia |
| Xu et al. | 2019 | CTGAN para datos tabulares |

---

## 📁 Archivos Generados

- `01_Data_Audit.ipynb` - Notebook de auditoría
- `01_Data_Audit_executed.ipynb` - Con outputs
- `dataset_diagnosis.json` - Reporte JSON completo (29KB)
- `RESULTADOS_DIAGNOSTICO.md` - Este archivo
