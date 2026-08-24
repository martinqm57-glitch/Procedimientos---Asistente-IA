# Asistente IA Interno de Procedimientos

Ecosistema de automatización desarrollado como proyecto final para integrar orquestación, base de datos, procesamiento con inteligencia artificial, validación humana y comunicación profesional.

## Descripción

El sistema responde consultas internas de empleados sobre procedimientos operativos, garantías, atención al cliente y escalamiento.

El workflow recibe una consulta mediante un Webhook, valida los datos, recupera procedimientos desde Notion, genera una respuesta estructurada con OpenAI y determina si puede responder directamente o si necesita aprobación humana.

Todas las ejecuciones quedan registradas en Notion y la respuesta final se envía por Gmail. El sistema también conserva el Gmail Thread ID para mantener la trazabilidad de la conversación.

## Tecnologías utilizadas

| Categoría | Tecnología | Función |
|---|---|---|
| Orquestador | n8n Cloud | Trigger, validaciones, bifurcaciones, esperas y control del flujo |
| Base de datos | Notion | Base de conocimiento, memoria de consultas y dashboard |
| Procesamiento IA | OpenAI - GPT-4o mini | Interpretación de la consulta y generación estructurada |
| Canal de salida | Gmail | Aprobación humana y comunicación final |

## Funcionamiento

1. Un Webhook POST recibe los datos de la consulta.
2. n8n valida los campos obligatorios.
3. Notion entrega los procedimientos disponibles.
4. n8n prepara un contexto dinámico para OpenAI.
5. GPT genera una respuesta estructurada mediante JSON Schema.
6. El flujo evalúa si requiere intervención humana.
7. Las respuestas seguras continúan por la ruta directa.
8. Las respuestas sensibles esperan aprobación o rechazo por Gmail.
9. El resultado se registra en Notion.
10. Gmail envía la respuesta final y n8n guarda el Thread ID.
11. El Webhook devuelve el objeto JSON final.

## RAG y memoria

El proyecto implementa una arquitectura RAG liviana. Antes de generar la respuesta, n8n recupera los procedimientos almacenados en Notion y los incorpora al prompt.

Notion cumple dos funciones:

- Base de conocimiento con los procedimientos internos.
- Memoria histórica de consultas, respuestas, fuentes, aprobaciones y errores.

## Human-in-the-loop

Cuando la información es insuficiente, contradictoria, de baja confianza o puede afectar al cliente, el sistema envía la propuesta por Gmail y espera la decisión del responsable.

- **Aprobada:** conserva la respuesta propuesta y continúa el flujo.
- **Rechazada:** reemplaza la propuesta por un mensaje de revisión manual.
- **Directa:** no requiere espera humana.

## Resiliencia

El workflow incluye rutas controladas para los principales errores:

- **HTTP 400:** datos obligatorios faltantes o inválidos.
- **HTTP 502:** fallo persistente de OpenAI después de los reintentos.
- Registro de errores en Notion.
- Reintentos automáticos del nodo de IA.
- Comparación explícita de valores booleanos.
- Trigger exclusivamente HTTP para evitar bucles con Gmail.

## Pruebas realizadas

Se comprobaron al menos las siguientes rutas:

- Camino directo.
- HITL aprobado.
- HITL rechazado.
- Datos obligatorios faltantes.
- Consulta sin conocimiento suficiente.
- Ejecución mediante la URL de producción.

## Entregables

### Documentación

- [Documentación final - Asistente IA](Documentacion/Documentación%20final%20-%20Asistente%20IA.pdf)
- [Evidencias operativas - Asistente IA](Documentacion/Evidencias%20operativas%20-%20Asistente%20IA.pdf)

### Workflow

- [Workflow n8n - Asistente IA](Workflow/Workflow%20n8n%20-%20Asistente%20IA.json)

El archivo fue sanitizado antes de su publicación. No contiene API keys, tokens, contraseñas, correos personales, URLs completas del Webhook ni referencias a credenciales configuradas.

Al importarlo en n8n será necesario configurar credenciales propias para Notion, OpenAI y Gmail.

## Enlaces públicos

- [Dashboard de Control - Notion](https://dandelion-hill-970.notion.site/Dashboard-de-Control-Asistente-IA-3c587cc475a4809197e4dcfa36f18659)
- [Base de Conocimiento - Notion](https://dandelion-hill-970.notion.site/3c487cc475a480a3a558e23ef2667328?v=3c487cc475a480b2b6bd000cd79d4164)
- [Video de muestra - Proyecto final](Documentation/Video%20de%20muestra%20-%20Proyecto%20final.mp4)

Los enlaces también se encuentran reunidos en [Enlaces del proyecto](Enlaces/Enlaces%20del%20proyecto.md).

## Seguridad y privacidad

- Los datos utilizados en las pruebas son ficticios.
- Las credenciales se administran dentro de n8n.
- El JSON publicado no contiene secretos.
- Las capturas públicas ocultan el correo personal y las URLs operativas.
- La vista pública de Notion evita exponer datos sensibles innecesarios.

## Alcance del prototipo

Para demostrar el funcionamiento extremo a extremo se cargaron cuatro procedimientos representativos. El diseño permite incorporar el manual completo agregando registros en Notion sin modificar la arquitectura principal.

## Estado del proyecto

Workflow construido, probado y publicado en n8n. Documentación, evidencias, dashboard y respaldo técnico completados. Solo resta incorporar el enlace al video demo final.
