# 📊 Resultados del Protocolo Experimental TSTR

**Fecha:** 2026-01-08  
**Versión:** v3_experimental  
**Fase:** Prompt 2 - Definición del Protocolo

---

## 📋 Resumen del Protocolo

| Aspecto | Definición |
|---------|------------|
| **Metodología** | Train on Synthetic, Test on Real (TSTR) |
| **Escenarios** | 4 (A: Baseline, B: Aug 1x, C: Aug 2x, D: Solo Synth) |
| **Métricas** | C-index, IBS |
| **Anti-Fuga** | Test set sellado hasta evaluación final |

---

## 🎯 Escenarios Definidos

| ID | Nombre | Composición | Propósito |
|----|--------|-------------|-----------|
| **A** | Baseline | 100% Real (n=296) | Línea base |
| **B** | Aug 1x | Real + 100% Synth | Duplicar datos |
| **C** | Aug 2x | Real + 200% Synth | Triplicar datos |
| **D** | Solo Synth | 100% Syntético | Prueba privacidad |

---

## ✅ Criterio de Éxito

```
C-index(Escenario_X) ≥ C-index(Baseline) - 0.02
```

> Si cualquier escenario sintético iguala o supera el baseline (con tolerancia de 0.02), el experimento es exitoso.

---

## 📚 Referencias Científicas

- Lawless (2003): Restricciones de dominio en survival
- Xu et al. (2019): CTGAN para datos tabulares
- Suresh et al. (2025): TSTR en datos clínicos

---

## 📁 Archivos Generados

| Archivo | Path |
|---------|------|
| Protocolo | `02_protocol/experimental_protocol.md` |

---

## ➡️ Siguiente Paso

**Prompt 3: Baseline Training** - Establecer la línea base de rendimiento.
