# Claves para generar un buen dataset

## Aplicable a todos los modelos

Como base hace falta mencionar que de base, independientemente del modelo, siempre se debe mantener cada dato de nuestro conjunto:

✔ Formato claro y repetible
✔ Conceptos bien aislados
✔ Evitar ambigüedad
✔ Repetición controlada

_Si el dataset está mal, ningún modelo lo arregla._

También tener en cuenta que cuanto **más chico el modelo, más rígido debe ser el dataset** y que cuanto **más grande el modelo, más importante es la instrucción**.

---

## Modelos pequeños

Ejemplos
- `TinyLlama 1.1B`
- `Phi-2`
- `Phi-3-mini`

### Cómo “piensan”

- Muy poco conocimiento implícito
- Generalizan mal
- Son extremadamente literales
- Sensibles al formato

### Reglas de dataset

**Formato obligatorio**

```
Pregunta:
Respuesta:
```
Siempre igual, sin variaciones.

**Alta redundancia**

Cada concepto debe significar como mínimo 6 filas del dataset:
- 2 definiciones
- 2 negaciones
- 1 “qué no es”
- 1 reformulación

En la práctica 8–10 filas por concepto funciona mejor.

Las negaciones explícitas son clave.

**Lo que NO funciona**

- Historias largas
- Respuestas abiertas
- Frases ambiguas
- Sinónimos no entrenados

### Conclusión

Con modelos chicos, el dataset define el comportamiento.

---

## Modelos medianos

Ejemplos
- `LLaMA` 2 / 3 (7B, 8B)
- `Mistral 7B`

### Cómo “piensan”

- Buen conocimiento general
- Pueden inferir contexto
- Mejor manejo de lenguaje natural
- Menos dependientes del formato exacto

### Reglas del dataset

**Formatos**
```
Pregunta:
Respuesta:
```

```
Instrucción:
Contexto:
Respuesta:
```

**Menos redundancia necesaria**

4–6 filas por concepto suelen ser suficientes.

Aun así, las negaciones siguen siendo importantes.

**Riesgo**

Si el dataset es vago o genérico el modelo rellena con conocimiento previo, no con tu dato verdadero.

### Conclusión

Estos modelos equilibran dataset + conocimiento previo.

---

## Modelos razonadores / técnicos (DeepSeek, Phi-3 Medium)

Ejemplos
- `DeepSeek`
- `Phi-3-medium`

### Cómo “piensan”
- Buscan patrones lógicos
- Siguen instrucciones
- Inferencia estructurada
- Menos verborragia

### Reglas de dataset
**Formato ideal**
```
Instrucción:
Responde de forma clara y concisa.

Entrada:
...

Respuesta:
...
```

También funciona bien con el formato Pregunta/Respuesta.

### Beneficio clave
- Menos ejemplos
- Más precisión lógica
- Buen manejo de exclusiones (“NO es…”)

### Conclusión

Funcionan mejor con instrucciones explícitas que con diálogo.

---

## Modelos grandes (GPT-4, GPT-4.1, GPT-5, Claude)

Ejemplos
- `GPT-4` / `GPT-4.1` / `GPT-5`
- `Claude 3.x`

### Cómo “piensan”
- Muchísimo conocimiento previo
- Generalizan muy bien
- Manejan contexto largo
- Pueden ignorar tu dataset si es débil

### Reglas de dataset

- Para estos modelos el dataset NO es suficiente
- Fine-tuning no siempre es necesario
- **RAG*** suele ser mejor

**Formato recomendado en caso de uso**

```
Sistema:
Eres un asistente corporativo de Midas Consultores.

Usuario:
...

Asistente:
...
```

También responde bien con conversaciones. **Por ejemplo**:

```
Context,Response
"Usuario: Hola
Asistente: Hola, ¿en qué puedo ayudarte?
Usuario: ¿Qué hace Midas?
Asistente: Midas Consultores es una empresa de consultoría tecnológica...",
"El asistente responde de forma clara y corporativa..."
```

También puede ser tipo Pregunta/Respuesta aunque es menos recomendado. NO es recomendado el texto libre.

### Conclusión

En modelos grandes, la instrucción manda más que el dataset.

---

## Resumen comparativo

| Modelo          | Dataset estricto | Negaciones | Instrucciones | Conversación |
| --------------- | ---------------- | ---------- | ------------- | ------------ |
| TinyLlama       | ⭐⭐⭐⭐⭐            | ⭐⭐⭐⭐⭐      | ❌             | ❌            |
| Phi (mini)      | ⭐⭐⭐⭐             | ⭐⭐⭐⭐       | ⚠️            | ❌            |
| LLaMA / Mistral | ⭐⭐⭐              | ⭐⭐⭐        | ⭐⭐⭐           | ⭐⭐           |
| DeepSeek        | ⭐⭐               | ⭐⭐⭐⭐       | ⭐⭐⭐⭐⭐         | ❌            |
| GPT / Claude    | ⭐                | ⭐⭐         | ⭐⭐⭐⭐⭐         | ⭐⭐⭐⭐⭐        |

---

## RAG*

### Definición corta

**RAG = Retrieval Augmented Generation**

El modelo NO aprende todo en el entrenamiento, sino que consulta información externa en el momento de responder.

### Sin RAG

```
Pregunta → Modelo → Respuesta
```

Problemas:
- Si no está en el dataset → inventa
- Reentrenar cuesta tiempo
- Con modelos chicos, falla mucho

### Con RAG

```
Pregunta
  ↓
Buscador (vector DB / embeddings)
  ↓
Contexto relevante
  ↓
Modelo
  ↓
Respuesta
```

### Por qué RAG es clave para modelos chicos

| Problema           | Dataset solo | Dataset + RAG |
| ------------------ | ------------ | ------------- |
| Poca info          | ❌            | ✅             |
| Cambios frecuentes | ❌            | ✅             |
| Alucinaciones      | ❌            | ✅             |
| Reentrenar         | Necesario    | No            |

