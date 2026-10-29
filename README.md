# Agentes con manos

**MCP, memoria y human-in-the-loop, construido en vivo.** Sesión técnica de **Mexico Tech Week 2026** (90 min con preguntas) con Jonathan Olvera, fundador de [Hiveflow](https://hiveflow.ai).

> Claude o Cursor son el control remoto. **Hiveflow es donde el agente vive, trabaja y rinde cuentas.** MCP es la puerta.

> Muchos agentes funcionan en el demo y se rompen en producción. Para que funcionen de verdad necesitan tres cosas: **herramientas confiables**, **memoria por proceso** y **un humano en el punto correcto**.

## Lo que construimos

```
💬 Chat público (QR)  →  🗄️ Hoja de clientes (Datos)  →  🧠 Agente decide (function calling)  →  🙋 ¿duda? Inbox · celular  →  📇 CRM
                                            todo dentro de Hiveflow
                         memoria por proceso · bitácora por acción · costo por nodo · cualquier modelo
```

Un agente de Hiveflow con su propio chat público: los clientes le piden una cotización, el agente conversa (pide nombre, empresa y correo sin repetir preguntas), consulta la hoja de clientes en Datos, decide con un LLM si es cliente nuevo, existente o dudoso, le contesta en el chat y lo registra en el CRM. No depende de Google: el chat, la hoja y el CRM viven en Hiveflow. Si es dudoso, **se detiene y pide aprobación** antes de escribir.

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

> Con Hiveflow crea el flujo «Cotizaciones por chat»: un chat público donde los clientes piden una cotización; un agente LLM que conversa, pide nombre, empresa y correo, consulta mi tabla «Clientes» en Datos, decide si es cliente nuevo, existente o dudoso y lo registra en el CRM de Hiveflow. Si es dudoso, que pida aprobación en el Inbox antes de escribir, y que siempre le conteste al cliente en el chat.

Hiveflow arma los nodos y las conexiones y te devuelve el link del flujo. Ábrelo en la app y publica el chat: el nodo de chat te da el link público (y lo puedes convertir en QR).

### 03 · Manos: herramientas y permisos
En el nodo LLM: function calling, la lista de herramientas (tabla «Clientes», CRM) y los permisos por herramienta. Regla de la sesión: **leer = permitir, escribir = preguntar**. El modelo no puede tocar nada que no esté en la lista.

### 04 · Memoria
Da tus datos de uno en uno en el chat: nunca vuelve a preguntar lo que ya dijiste. Abre el chat en otro celular y di que eres la misma persona: te reconoce por su CRM. Una conversación nunca ve los datos de otra. La memoria es **por proceso** y **aislada por cliente**: un cliente nunca ve datos de otro.

### 05 · Frenos (human-in-the-loop)
Escribe como un cliente dudoso (dices ser de una empresa de la hoja con un correo de otro dominio, o pides 60 % de descuento). El agente te contesta «en revisión» y el flujo se pausa y te avisa en el Inbox de Hiveflow y en el celular. Apruébala desde la app, o desde tu Claude por MCP:

> ¿Qué aprobaciones tengo pendientes?

> Apruébala.

El flujo continúa con la acción que pidió.

### 06 · Romperlo
Desconecta la tabla «Clientes» del agente (o quítale el permiso) y manda otro mensaje. En la vista de ejecuciones de Hiveflow revisa en qué nodo falló, con qué error, qué sí quedó escrito y cómo reintentarlo.

## 3. Operarlo

- **Terminal:** con Remote Control de la CLI de Hiveflow ves y operas el agente en vivo; pausar o detener es un comando.
- **Bitácora:** en Hiveflow, cada acción con su decisión de política (permitida, preguntada, denegada) y quién aprobó. También se la puedes pedir a tu Claude por MCP: *«Muéstrame la bitácora del agente»*.
- **Costos:** tokens y costo por nodo y por proceso en Analítica.

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

Crea en Hiveflow (Datos) la tabla «Clientes» con las filas de [ejemplos/clientes.csv](ejemplos/clientes.csv) (datos inventados) y conéctala al nodo LLM como herramienta: el agente puede consultarla, y con permiso, escribir en ella.

## Enlaces

- Hiveflow: [hiveflow.ai](https://hiveflow.ai) · App: [app.hiveflow.ai](https://app.hiveflow.ai)
- Agenda 20 minutos con Jonathan: [calendly.com/hiveflow](https://calendly.com/hiveflow/)
- Jonathan en [Instagram](https://instagram.com/johnolven), [TikTok](https://tiktok.com/@JohnOlven) y [LinkedIn](https://linkedin.com/in/johnolven)

---

Hecho con [Hiveflow](https://hiveflow.ai) para Mexico Tech Week 2026.
