# Agentes con manos

**MCP, memoria y human-in-the-loop, construido en vivo.** Sesión técnica de **Mexico Tech Week 2026** (90 min con preguntas) con Jonathan Olvera, fundador de [Hiveflow](https://hiveflow.ai).

> **Tu Claude es el arquitecto; Hiveflow es donde el agente vive.** Por MCP, Claude crea y opera el agente; Hiveflow le da herramientas con permisos, memoria por proceso y frenos humanos.

> Muchos agentes funcionan en el demo y se rompen en producción. Para que funcionen de verdad necesitan tres cosas: **herramientas confiables**, **memoria por proceso** y **un humano en el punto correcto**.

## Lo que construimos

```
💬 Chat (link y QR)  →  🧠 Agente (function calling)  →  🙋 ¿duda? Inbox · celular  →  📇 CRM
                          │ herramientas:                     └→ 💬 respuesta al cliente
                          ├─ 🗄️ tabla «clientes» (solo consultar)
                          └─ 📇 tabla «leads»: su memoria (solo consultar)
          memoria por proceso · bitácora por acción · costo por nodo · cualquier modelo
```

Un agente como los de WhatsApp, pero con chat público: los clientes le piden una cotización, el agente conversa (pide nombre, empresa y correo sin repetir preguntas), consulta la tabla de clientes, decide con un LLM si es cliente nuevo, existente o dudoso, le contesta en el chat y lo registra en el CRM. Si es dudoso, **se detiene y pide aprobación** antes de escribir. Todo vive en Hiveflow: el flujo crea solo sus tablas (10 clientes inventados) con el primer mensaje.

## Hazlo tú (15 minutos)

### 1 · Crea tu cuenta
[app.hiveflow.ai/signup](https://app.hiveflow.ai/signup) — trae 5 USD de crédito para usarlo hoy.

### 2 · Conecta tu Claude a Hiveflow (MCP)
- **Claude (claude.ai o la app):** Personalizar → Conectores → Agregar conector personalizado → `https://mcp.hiveflow.ai` → inicia sesión y autoriza.
- **Claude Code:** `claude mcp add --transport http hiveflow https://mcp.hiveflow.ai` y luego `/mcp` para autorizar.
- **Cursor:** en `~/.cursor/mcp.json` → `{ "mcpServers": { "hiveflow": { "url": "https://mcp.hiveflow.ai" } } }`.

Prueba: *«¿Qué herramientas tienes de Hiveflow?»*

### 3 · Pídele a tu Claude que cree el agente
Copia y pega:

> Lee https://raw.githubusercontent.com/hiveflowai/agentes-con-manos/main/ejemplos/flujo-agente.json. Con Hiveflow, crea ese flujo con create_flow usando sus nodes y edges tal cual, pero antes reemplaza TOKEN_UNICO (en publicToken y en publicUrl) por 14 letras minúsculas y números al azar. Después actívalo con set_flow_state y dame el link del flujo y el link de mi chat: https://app.hiveflow.ai/public/chat/<el token que pusiste>.

El flujo completo está en [ejemplos/flujo-agente.json](ejemplos/flujo-agente.json). Si tu Claude no puede abrir links, pégale el contenido del archivo en lugar del link.

### 4 · Abre tu chat y pídele una cotización
Abre el link de tu chat (en el celular o en la compu). Di que eres de una empresa de [ejemplos/clientes.csv](ejemplos/clientes.csv), por ejemplo «Soy Ana Ruiz de Distribuidora Norte, ana@distnorte.example, quiero 40 sillas». Revisa en Hiveflow → **Datos → leads**: ahí quedó registrada.

## Lo que pasa por dentro

### Manos: herramientas y permisos
El agente tiene dos herramientas: **consultar_clientes** y **consultar_leads**. Guardar, actualizar y borrar están **denegados**: el modelo no puede tocar nada que no esté permitido, y eso no lo decide el modelo: lo decide Hiveflow. Lo que se escribe en el CRM lo escriben los nodos del flujo, no el modelo.

### Memoria
Da tus datos de uno en uno: nunca vuelve a preguntar lo que ya dijiste (historial del chat). Abre el chat en otro celular y di que eres la misma persona: te reconoce por su CRM. Una conversación nunca ve los datos de otra.

### Frenos (human-in-the-loop)
Escribe como un cliente dudoso (dices ser de una empresa de la hoja con un correo de otro dominio, o pides más de su crédito). El agente te contesta «en revisión» y el flujo se pausa y te avisa en el Inbox de Hiveflow y en el celular. Apruébala desde la app o el celular: el lead entra al CRM. Si la rechazas, el flujo se detiene y no se escribe nada.

### Romperlo
En la herramienta «Clientes» del agente cambia `consultar` a «denegar» y manda otro mensaje. En la vista de ejecuciones de Hiveflow: el agente ya no puede consultar y lo dice; nada se escribió a ciegas. Regresa el permiso y reintenta.

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

[ejemplos/clientes.csv](ejemplos/clientes.csv): los 10 clientes inventados que el flujo carga solo en tu tabla «clientes».

## Enlaces

- Hiveflow: [hiveflow.ai](https://hiveflow.ai) · App: [app.hiveflow.ai](https://app.hiveflow.ai)
- Agenda 20 minutos con Jonathan: [calendly.com/hiveflow](https://calendly.com/hiveflow/)
- Jonathan en [Instagram](https://instagram.com/johnolven), [TikTok](https://tiktok.com/@JohnOlven) y [LinkedIn](https://linkedin.com/in/johnolven)

---

Hecho con [Hiveflow](https://hiveflow.ai) para Mexico Tech Week 2026.
