# Respuesta al Diagnóstico Inicial: Definición de Variables y Vector de Características

## 1. Definición del Evento y Censura ($T$ y $E$)

### 1.1 El Origen ($t_0$): Punto de Partida

Según lo acordado previamente y basándome en el corpus (DOI: 10.1111/ejed.12525 - *"The transition from higher education to first employment in Spain"*):

> **$t_0$ = Inicio del último semestre** (no la graduación formal)

**Justificación del corpus**: El paper de Getie Ayaneh et al. (DOI: 10.1155/2020/8653405) define $t_0$ como "graduation date" pero advierte sobre el sesgo de **"immortal time bias"** cuando los estudiantes buscan empleo antes de graduarse. En el caso de la EPN, muchos estudiantes buscan empleo mientras cursan el último semestre.

**Para la encuesta específica**: La encuesta se aplicó ~6 meses después de culminar estudios, y las preguntas capturan:
- *"¿Cuánto tiempo lleva trabajando?"* → para quienes trabajan
- *"¿Por cuánto tiempo has estado buscando trabajo?"* → para censurados

Esto implica que **ya se captura implícitamente el tiempo desde que comenzaron a buscar**, que típicamente es el último semestre.

---

### 1.2 El Evento ($E=1$): Definición de "Primer Empleo"

Basándome en:

#### Definición INEC de Empleo Adecuado en Ecuador
- Ingresos ≥ salario mínimo
- Jornada ≥ 40 horas (o <40 sin desear más)

#### Correspondencia STEM (Columna 16 de la encuesta)
Escala 1-4 donde `1=sin relación, 4=totalmente relacionado`

| Valor | Frecuencia |
|-------|------------|
| 4 (totalmente relacionado) | 87 |
| 3 (relacionado) | 82 |
| 2 (poco relacionado) | 29 |
| 1 (sin relación) | 11 |

**Propuesta operacional para $E=1$**:

```python
E = 1 si:
    trabaja_actualmente == "Si" AND
    modalidad_trabajo in ["Tiempo completo", "Medio tiempo"] AND
    correspondencia_formacion >= 3  # Al menos "relacionado"
```

> **Del corpus** (DOI: 10.1371/journal.pone.0318167): *"For employability prediction in STEM, we define employed as having a job **related to their field of study**."*

**Implicación**: Si un ingeniero trabaja de cajero mientras busca → **NO cuenta como evento** (sigue censurado).

---

### 1.3 Censura ($E=0$): Tratamiento de Casos

| Caso | N | % | Tratamiento | Justificación |
|------|---|---|-------------|---------------|
| No trabaja + busca activamente | 162 | 42.6% | Censura a la derecha | Estándar en survival analysis |
| No trabaja + NO busca (inactivos) | 9 | 2.4% | **Excluir del análisis** | Alternativa: censurar al tiempo máximo observado |
| Entró a maestría tiempo completo | ? | ? | Riesgo competitivo (o excluir) | No disponible en la encuesta |

> **Del corpus** (DOI: 10.32609/j.ruje.10.128611 - Russia unemployment duration): *"Individuals who exit the labor force for reasons other than employment (education, retirement) are treated as competing risks or censored at exit."*

---

## 2. Sobre el Vector Híbrido $[V_{técnico} (52D) + V_{blando} (7D)]$

### 2.1 Origen de las 52 Dimensiones Técnicas

**NO son embeddings ni PCA**. Son **52 variables binarias (one-hot encoding)** de competencias técnicas específicas.

#### Metodología de construcción

1. **Diccionario Maestro** basado en taxonomías internacionales:
   - O*NET Technology Skills
   - ESCO European Competences
   - IEEE SWEBOK (18 knowledge areas)

2. **Matching de texto**: Las respuestas abiertas (columna 35: *"¿Qué asignaturas te resultaron más relevantes?"*) se procesan con ~400 aliases para detectar presencia/ausencia de cada competencia.

```
V_técnico[i] = 1 si el estudiante menciona algún alias de la competencia i
             = 0 en caso contrario
```

**Cobertura validada**: 96.6% de las respuestas tienen al menos 1 match.

---

### 2.2 Maldición de la Dimensionalidad: Análisis de Riesgo

| Métrica | Valor |
|---------|-------|
| N total | 380 |
| Features totales | 59 (7 blandas + 52 técnicas) |
| **Ratio N/p** | **6.4** |

> **Del corpus** (DOI: 10.1007/s10994-014-9468-3 - Feature selection for survival): *"A ratio of N/p ≥ 10 is generally recommended for Cox models. For tree-based methods like RSF, the ratio can be lower due to their regularization properties."*

**Riesgo**: Con N/p = 6.4 estamos en **zona límite**.

#### Mitigaciones recomendadas (del corpus)

| Estrategia | Referencia (DOI) | Descripción |
|------------|------------------|-------------|
| Feature selection | 10.1007/s10994-014-9468-3 | Usar C-index para rankear y seleccionar top-k features |
| Regularización | 10.1186/s12874-024-02390-4 | Elastic Net en Cox-PH |
| **Usar RSF** | 10.1186/s12911-025-02951-7 | *"RSF handles high-dimensional data better than Cox due to its bagging mechanism"* |

**Recomendación**: Reducir las 52D técnicas a ~15-20 usando:
- Eliminar competencias con <10 menciones
- Agrupar por categoría (computing, electrical, mechanical, etc.)

---

### 2.3 Integración: Método de Combinación

**Concatenación simple**:

```python
V_total = concat(V_blandas_norm, V_tecnicas_binary)
# Shape: (N, 59)
```

#### Consideración de escala

| Vector | Dimensiones | Rango |
|--------|-------------|-------|
| V_blandas | 7 | [0, 1] (normalizado) |
| V_técnicas | 52 | {0, 1} (binario) |

> **Del corpus** (DOI: 10.1038/s41598-021-86327-7 - XGBoost for survival): *"For tree-based models like RSF and XGBoost, feature scaling is not strictly necessary as splits are based on relative ordering."*

- **Para RSF/XGBoost**: No hay problema de escala
- **Para Cox-PH**: Considerar estandarizar todo a z-scores

---

### 2.4 Validación de Habilidades Blandas: Fuente de Ruido

Las 7 habilidades blandas provienen de **autoevaluaciones** (escala Likert 1-5):

| Variable | Descripción |
|----------|-------------|
| `hab_gestion` | Gestionar proyectos complejos |
| `hab_comunicacion` | Presentar en español e inglés |
| `hab_liderazgo` | Dirigir equipos |
| `hab_trabajo_equipo` | Participar en equipos |
| `hab_etica` | Responsabilidad ética |
| `hab_responsabilidad` | Responsabilidad social |
| `hab_aprendizaje` | Aprendizaje continuo |

> **Del corpus** (DOI: 10.1109/ACCESS.2021.3059516): *"Self-reported soft skills are subject to social desirability bias, but remain the most practical method in educational research."*

#### Mitigación del ruido

1. La normalización [0,1] reduce impacto de valores extremos
2. RSF es robusto a ruido en features individuales (promedia muchos árboles)
3. Considerar usar solo el **primer componente principal** de las 7 blandas si hay alta colinealidad

---

## 3. Resumen de Decisiones Propuestas

| Aspecto | Decisión | Referencia (DOI) |
|---------|----------|------------------|
| $t_0$ | Inicio último semestre | 10.1155/2020/8653405 |
| $E=1$ | Trabaja + correspondencia ≥ 3 | Definición INEC + 10.1371/journal.pone.0318167 |
| Inactivos (N=9) | Excluir | 10.32609/j.ruje.10.128611 |
| Reducción 52D | Top-20 por frecuencia | 10.1007/s10994-014-9468-3 |
| Modelo primario | **RSF** | 10.1186/s12911-025-02951-7 |
| Modelo desafiante | XGBoost-AFT | arXiv:2006.04920 |
| Línea base | Cox-PH | 10.1155/2021/6698656 |

---

## 4. Datos de la Muestra

```
N total:                     380
├── Trabaja (event=1):       209 (55.0%)
├── Busca empleo (event=0):  162 (42.6%)
└── Inactivos (excluir):       9 (2.4%)

Carreras STEM:               27 únicas
Habilidades blandas:          7 dimensiones
Habilidades técnicas:        52 dimensiones
Vector total:                59 features
Ratio N/p:                   6.4
```
