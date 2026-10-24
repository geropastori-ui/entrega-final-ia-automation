# Entrega Final - Pipeline de contenido con revisión humana

Proyecto integrador de la Especialización en IA Automation (CoderHouse). Automatiza la generación de posts de LinkedIn: una idea semilla cargada en Airtable pasa por un escenario de Make que recupera contexto (RAG), genera el borrador con un modelo de Groq, espera la aprobación de una persona y, una vez aprobado, envía el aviso por Gmail y marca la pieza como publicada. Los fallos se registran en una tabla vinculada y se ven en un dashboard público.

## Contenido del repositorio

| Archivo | Qué es |
|---|---|
| `01_Diagrama_de_arquitectura.pdf` | Mapa de arquitectura: disparador, router, APIs, nodo de IA, rutas de error y destino. |
| `02_Arquitectura.pdf` | Descripción de componentes, rutas, control de bucles y pruebas. |
| `03_Manual_de_datos.pdf` | Esquema de tablas vinculadas y esquemas JSON entre módulos. |
| `04_Matriz_de_costos.pdf` | Decisión de modelo, precios vigentes, Batch (-50 %) y caché. |
| `05_Seguridad_y_resiliencia.pdf` | Credenciales, privacidad, revisión humana y manejo de errores. |
| `Entrega_Final_PASTORI.blueprint.json` | Blueprint exportado del escenario de Make. No contiene claves. |
| `screenshots/` | Capturas: escenario de Make, historial de ejecuciones, falla de IA con ejecución incompleta, Log de errores y tabla Centro de control. |

## Enlaces

- Dashboard público (Shared View, solo lectura): https://airtable.com/applR5wde8nycBbLW/shr4RLhPLx0Q3k2Ty
- Log de errores (Shared View, solo lectura): https://airtable.com/applR5wde8nycBbLW/shrHYyKaKqiYGUZ6E
- Video de demostración: _pendiente de agregar_

## Cómo funciona

1. La persona carga una **Idea Semilla** en la tabla Centro de control (Estado = Generando).
2. Make detecta el registro y el Router lo dirige:
   - **Generando con idea**: busca contexto en Base RAG, lo une y llama a Groq. El borrador queda en "En revisión".
   - **Generando sin idea**: registra "Datos faltantes" en Log de errores y marca Error.
   - **Aprobado con borrador**: envía el Gmail y marca "Publicado".
3. Una persona revisa el borrador y cambia el Estado a "Aprobado". Es el punto de control humano.
4. Si Groq falla, el manejador de errores registra el fallo, marca Error y reintenta con Break (3 veces, cada 5 minutos).

## Nota sobre el modelo

Se usó **Groq** (openai/gpt-oss-120b) por recomendación de los docentes y porque las APIs de OpenAI y Anthropic son de pago. La matriz de costos compara con GPT-4o mini y Claude Haiku 4.5 para mostrar qué cambiaría al escalar. Groq es el proveedor de inferencia, no Grok de xAI.

## Cómo reproducirlo

1. Crear en Airtable la base con las tablas descritas en `03_Manual_de_datos.pdf`.
2. En Make, importar `Entrega_Final_PASTORI.blueprint.json` (Create scenario > Import blueprint) y volver a conectar Airtable, Groq y Gmail con tus propias credenciales.
3. Ajustar los IDs de base y tabla si cambian, y probar con una idea semilla.

## Limitaciones

El destinatario del correo está fijo en el módulo; el cambio a "Aprobado" es manual; el plan gratuito de Groq tiene límite de tokens por minuto; la tasa de error se calcula sobre pocas piezas de prueba.
