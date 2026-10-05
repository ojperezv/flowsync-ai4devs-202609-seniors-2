# Registro de Prompts — Ejercicio Spec Viva (FlowSync)

- **Herramienta:** GitHub Copilot (Eclipse - Agent)
- **Modelo:** Claude 5.5 Sonnet
- **Pega el prompt tal cual lo lanzaste:**
  > `Inspecciona el código fuente actual de backend y frontend enfocado exclusivamente en el vertical de Cuentas y Acceso (registro, login, sesión y perfil). Redacta el apartado "## Purpose" en una o dos frases en español explicando para qué existe esta capability en FlowSync. No modifiques código ni propongas cambios.`
- **Lo que no funcionó:**
  *En modo "Auto" la herramienta arrojó el error "No model configuration found"; el problema se resolvió cambiando manualmente el modelo a Claude 3.5 Sonnet en las preferencias de Copilot.*

---

- **Herramienta:** GitHub Copilot (Eclipse - Agent)
- **Modelo:** Claude 5.5 Sonnet
- **Pega el prompt tal cual lo lanzaste:**
  > `Basándote en el código actual del backend (rutas, controladores, validadores) y frontend (pantallas, estados), lista los requisitos del vertical bajo "## Requirements" utilizando "### Requirement: [Nombre]". Expresa cada uno indicando que el sistema SHALL hacer algo. No esta permitido incluir nombres de clases, archivos o endpoints; Describiendo únicamente lo que el usuario ve o la API responde.`
- **Lo que no funcionó:**
  *El agente organizó en su primera respuesta los requisitos dividiendo el backend y el frontend en secciones separadas. Se tuvo que solicitar que unificara ambos bloques bajo el comportamiento observable único del sistema.*

---

- **Herramienta:** GitHub Copilot (Eclipse - Agent)
- **Modelo:** Claude 5.5 Sonnet
- **Pega el prompt tal cual lo lanzaste:**
  > `Para cada uno de los requisitos definidos, genera los escenarios bajo "#### Scenario: [Título]". Usa exactamente dos viñetas por escenario: "- **WHEN**" y "- **THEN**". Incluye las condiciones previas dentro del WHEN. Queda strictly prohibido usar etiquetas GIVEN o términos del delta como ADDED, MODIFIED o REMOVED.`
- **Lo que no funcionó:**
  *El asistente incluyó por defecto un bloque previo de contexto antes del WHEN. Se le dio la instrucción de integrar ese punto de partida dentro de la misma frase del WHEN para mantener la regla de solo dos viñetas.*

---

- **Herramienta:** GitHub Copilot (Eclipse - Agent)
- **Modelo:** Claude 5.5 Sonnet
- **Pega el prompt tal cual lo lanzaste:**
  > `Audita los escenarios de respuesta ante errores en el frontend y backend para el login y registro. Asegúrate de que no existan contradicciones entre los códigos HTTP (400, 401, 422) y lo que muestra la interfaz, y que se mantenga el formato de solo dos viñetas por escenario.`
- **Lo que no funcionó:**
  *El agente detectó dos puntos de incertidumbre no verificados en el código (manejo de nombres vacíos y normalización de mayúsculas en emails), además de incluir notas aclaratorias fuera de la estructura de los escenarios.*

---

- **Herramienta:** GitHub Copilot (Eclipse - Agent)
- **Modelo:** Claude 5.5 Sonnet
- **Pega el prompt tal cual lo lanzaste:**
  > `Consolida todo el documento en el archivo docs/spec-viva/OV.md. Incluye únicamente la sección Purpose y el listado de Requirements con sus Scenarios bajo las reglas estrictas (RFC keywords, 2 viñetas por escenario, sin deltas ni notas de deuda técnica).`
- **Lo que no funcionó:**
  *Se borró el escenario del e-mail en mayúsculas por no estar confirmado en el código, y se ajustó el mensaje de error cuando falla la conexión al abrir la aplicación*
