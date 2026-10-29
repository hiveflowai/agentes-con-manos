# Agentes con manos

**MCP, memoria y human-in-the-loop, construido en vivo.** Sesión técnica de **Mexico Tech Week 2026** (90 min con preguntas) con Jonathan Olvera, fundador de [Hiveflow](https://hiveflow.ai).

> **MCP son las manos del agente** (sus herramientas: hoy Google Sheets; mañana Airtable o tu ERP). **Hiveflow decide qué puede tocar cada mano**, guarda su memoria y le pone frenos.

> Muchos agentes funcionan en el demo y se rompen en producción. Para que funcionen de verdad necesitan tres cosas: **herramientas confiables**, **memoria por proceso** y **un humano en el punto correcto**.

## Lo que construimos

```
💬 Chat (QR)  →  🧠 Agente (function calling)  →  🙋 ¿duda? Inbox · celular  →  📇 CRM
                   │ manos por MCP:                       └→ 💬 respuesta al cliente
                   └─ 📊 Google Sheets «Clientes» (solo leer)
          memoria por proceso · bitácora por acción · costo por nodo · cualquier modelo
```

Un agente como los de WhatsApp, pero con chat público (link y QR): los clientes le piden una cotización, el agente conversa (pide nombre, empresa y correo sin repetir preguntas), **lee tu Google Sheet de clientes por MCP**, decide con un LLM si es cliente nuevo, existente o dudoso, le contesta en el chat y lo registra en el CRM. Si es dudoso, **se detiene y pide aprobación** antes de escribir.

## 1. Prepara tu hoja de clientes

Crea un Google Sheet con una pestaña **«Clientes»** y pega [ejemplos/clientes.csv](ejemplos/clientes.csv) (Archivo → Importar). Son 10 clientes inventados con su estado (`cliente`, `prospecto`, `suspendido`) y su límite de crédito. Copia el **ID de la hoja** (lo que va entre `/d/` y `/edit` en la URL).

## 2. Los 6 pasos

### 01 · Conectar Google Sheets (MCP)
Crea tu cuenta en [app.hiveflow.ai](https://app.hiveflow.ai/signup) → **Integraciones → Google Sheets** → inicia sesión con Google y autoriza. Es un servidor MCP: le da al agente herramientas como `get_sheet_data` (leer) y `update_cells` (escribir). Sin llaves ni contraseñas.

### 02 · El agente: Chat → Agente → Chat
En el canvas: **Chat de entrada → Agente (LLM) → Chat de salida**, igual que un agente de WhatsApp. En el objetivo del agente pega [ejemplos/prompt-agente.md](ejemplos/prompt-agente.md) con el ID de tu hoja. Publica el chat: el nodo te da el link público (conviértelo en QR).

### 03 · Manos: herramientas y permisos
Conecta el nodo **Google Sheets (MCP)** al punto verde del agente y abre sus permisos: **`get_sheet_data` = permitir; `update_cells` y las demás escrituras = denegar**. El modelo no puede tocar nada que no esté permitido, y eso no lo decide el modelo: lo decide Hiveflow.

### 04 · Memoria
Da tus datos de uno en uno en el chat: nunca vuelve a preguntar lo que ya dijiste. Abre el chat en otro celular y di que eres la misma persona: te reconoce por su CRM. Una conversación nunca ve los datos de otra. La memoria es **por proceso** y **aislada por cliente**.

### 05 · Frenos (human-in-the-loop)
Escribe como un cliente dudoso (dices ser de una empresa de la hoja con un correo de otro dominio, o pides más de su crédito). El agente te contesta «en revisión» y el flujo se pausa y te avisa en el Inbox de Hiveflow y en el celular. Apruébala desde la app o el celular: el lead entra al CRM. Si la rechazas, no se escribe nada.

### 06 · Romperlo
En el nodo Google Sheets cambia `get_sheet_data` a «denegar» y manda otro mensaje. En la vista de ejecuciones de Hiveflow: el agente ya no puede leer la hoja y lo dice; nada se escribió a ciegas. Regresa el permiso y reintenta.

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

[ejemplos/clientes.csv](ejemplos/clientes.csv): 10 clientes inventados para tu pestaña «Clientes» de Google Sheets.

## Enlaces

- Hiveflow: [hiveflow.ai](https://hiveflow.ai) · App: [app.hiveflow.ai](https://app.hiveflow.ai)
- Agenda 20 minutos con Jonathan: [calendly.com/hiveflow](https://calendly.com/hiveflow/)
- Jonathan en [Instagram](https://instagram.com/johnolven), [TikTok](https://tiktok.com/@JohnOlven) y [LinkedIn](https://linkedin.com/in/johnolven)

---

Hecho con [Hiveflow](https://hiveflow.ai) para Mexico Tech Week 2026.
