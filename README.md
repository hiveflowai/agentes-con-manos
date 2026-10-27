# Agentes con manos

**MCP, memoria y human-in-the-loop, construido en vivo.** Sesión técnica de **Mexico Tech Week 2026** con Jonathan Olvera, fundador de [Hiveflow](https://hiveflow.ai).

> Muchos agentes funcionan en el demo y se rompen en producción. Para que funcionen de verdad necesitan tres cosas: **herramientas confiables**, **memoria por proceso** y **un humano en el punto correcto**.

## Lo que construimos

```
📨 Correo  →  📊 Sheets (MCP)  →  🧠 LLM decide (function calling)  →  🙋 ¿duda? aprobación  →  📇 CRM
                         memoria por proceso · bitácora por acción · costo por nodo · cualquier modelo
```

Un agente que lee un correo de solicitud, consulta la hoja de clientes, decide con un LLM si es cliente nuevo, existente o dudoso, y lo registra en un CRM. Si es dudoso, **se detiene y pide aprobación** antes de escribir.

## 1. Conecta tu Claude, Cursor o Claude Code a Hiveflow (MCP)

Crea tu cuenta en [app.hiveflow.ai](https://app.hiveflow.ai/signup) y conecta el servidor MCP remoto `https://mcp.hiveflow.ai` (te pide autorizar con tu cuenta):

**Claude (claude.ai o la app):** Personalizar → Conectores → Agregar conector personalizado → `https://mcp.hiveflow.ai`.

**Claude Code:**

```bash
claude mcp add --transport http hiveflow https://mcp.hiveflow.ai
```

**Cursor:** en `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "hiveflow": { "url": "https://mcp.hiveflow.ai" }
  }
}
```

Prueba: pregúntale *«¿Qué herramientas tienes de Hiveflow?»*.

## 2. Los 6 pasos (con los prompts de la sesión)

### 01 · Conectar
Lo de arriba. Revisa que el modelo vea las herramientas de Hiveflow (crear y correr flujos, aprobaciones, bitácora, tableros).

### 02 · Pedir el flujo

> Con Hiveflow crea el flujo «Leads por correo»: cuando llegue un correo con «cotización» en el asunto, lee mi hoja «Clientes» de Google Sheets, decide con un LLM si es cliente nuevo, existente o dudoso, y regístralo en un CRM. Si es dudoso, que pida aprobación antes de escribir.

El modelo arma nodos y conexiones por MCP y te devuelve el link del flujo. Ábrelo en la app.

### 03 · Manos: herramientas y permisos
En el nodo LLM: function calling, la lista de herramientas (Sheets por MCP, CRM) y los permisos por herramienta. Regla de la sesión: **leer = permitir, escribir = preguntar**. El modelo no puede tocar nada que no esté en la lista.

### 04 · Memoria
Manda dos correos del mismo remitente: el segundo no vuelve a preguntar lo que ya sabe. La memoria es **por proceso** y **aislada por cliente**: un cliente nunca ve datos de otro.

### 05 · Frenos (human-in-the-loop)
Manda un correo dudoso. El flujo se pausa y te avisa (Inbox, celular). Desde tu Claude:

> ¿Qué aprobaciones tengo pendientes?

> Apruébala.

El flujo continúa con la acción que pidió.

### 06 · Romperlo
Renombra la pestaña de la hoja o quita el permiso de Sheets y manda otro correo. Revisa en qué nodo falló, con qué error, qué sí quedó escrito y cómo reintentarlo.

## 3. Operarlo

- **Bitácora:** pregúntale a tu Claude *«Muéstrame la bitácora del agente»*: cada acción, la decisión de política (permitida, preguntada, denegada) y quién aprobó.
- **Costos:** tokens y costo por nodo y por proceso en Analítica.
- **Terminal:** con Remote Control operas el agente desde la terminal.

## El patrón, con cualquier modelo

1. **Herramientas con contrato y permisos:** leer, preguntar o denegar por herramienta; errores legibles para el modelo.
2. **Memoria por proceso, no por modelo:** aislada por cliente; el contexto no es una base de datos.
3. **El humano decide en el riesgo, no en todo:** umbral claro; lo demás corre solo.
4. **Cada paso se puede reintentar** sin duplicar lo que ya se escribió.
5. **Todo auditable y con costo:** acción, decisión, quién aprobó y cuánto costó.

## Preguntas frecuentes

**¿MCP o API directa?** MCP cuando el modelo debe descubrir y usar herramientas; API cuando el paso es fijo. Hiveflow expone las dos.

**¿Funciona con cualquier modelo?** El patrón no depende del modelo. En la sesión usamos Claude.

**¿Y el prompt injection?** El agente solo puede usar las herramientas que le diste, con sus permisos; lo sensible se pregunta a una persona y todo queda en bitácora. No es infalible: por eso hay frenos.

**¿Dónde viven los datos y las llaves?** En Hiveflow. Los tokens de tus integraciones no viajan al modelo.

**¿Cuánto cuesta?** Precios públicos en [hiveflow.ai](https://hiveflow.ai). El consumo de IA va por créditos y se ve por nodo.

## Datos de prueba

Usa [ejemplos/clientes.csv](ejemplos/clientes.csv) como tu hoja «Clientes» (datos inventados).

## Enlaces

- Hiveflow: [hiveflow.ai](https://hiveflow.ai) · App: [app.hiveflow.ai](https://app.hiveflow.ai)
- Agenda 20 minutos con Jonathan: [calendly.com/hiveflow](https://calendly.com/hiveflow/)
- Jonathan en [Instagram](https://instagram.com/johnolven), [TikTok](https://tiktok.com/@JohnOlven) y [LinkedIn](https://linkedin.com/in/johnolven)

---

Hecho con [Hiveflow](https://hiveflow.ai) para Mexico Tech Week 2026.
