Modulo 2: Specs Driven Development


Para demostrar la efectividad del desarrollo guiado por especificaciones, propongo la creación de un Generador de Slugs robusto. Utilizaremos Claude Code (o Cursor en modo Composer) como herramienta principal, ya que permite orquestar de forma explícita los tres pilares del paradigma agéntico actual.
Análisis Técnico
a) Micro-tarea: Generador de Slugs (SEO-Ready)
El objetivo es transformar un título (ej: "¡Introducción a la IA en 2026!") en un slug válido para URL ("introduccion-a-la-ia-en-2026"). Esta tarea, aunque parece trivial, es excelente para SDD porque requiere:
Manejo de regex para caracteres no alfanuméricos.
Tratamiento de acentos y tildes (normalización Unicode).
Validación mediante Test-Driven Development (TDD).
b) Aplicación de los 3 Pilares en VS Code
La herramienta recomendada es Claude Code (ejecutada en el terminal integrado de VS Code) o Cursor (modo Composer). Ambas permiten saltar del modo "completado" al modo "agéntico" (L3) necesario para aplicar los pilares con rigor.
1. Herramienta (Harness Engineering): Utilizaremos el modelo Claude 3.5/3.7 Sonnet dentro del arnés de Claude Code. Este arnés proporciona herramientas de lectura/escritura de archivos y ejecución de comandos Bash


Ejercicio 1:  Email-validator
Propongo la creación de un Validador de Emails basado en Estándares (RFC 5322). El uso del Perplexity MCP es disruptivo aquí porque permite al agente de código salir del "conocimiento estático" de sus pesos y consultar en tiempo real las especificaciones técnicas vigentes, evitando el uso de expresiones regulares simplistas que fallan en entornos de producción.

Análisis Técnico
a) Micro-tarea: Validador de Email "RFC-Compliant"
A diferencia de un validador trivial, este debe cumplir con:
Longitud máxima: 254 caracteres totales [RFC 5321].
Parte local: Manejo de caracteres especiales permitidos (ej. !#$%&'*+/=?^_{|}~-) y puntos no consecutivos.
Dominios: Soporte para direcciones IP literales (ej. user@[192.168.2.1]) y validación de TLDs (Top-Level Domains).
Internacionalización (IDN): Manejo opcional de caracteres no ASCII.

b) Aplicación de los 3 Pilares con Perplexity MCP
Utilizaremos Claude Code o Cursor (modo Composer) como orquestador, integrando el servidor Perplexity MCP para el pilar de Contexto.
1. Herramienta (Harness Engineering):
Stack: VS Code + Perplexity MCP + Claude 3.7 Sonnet (como razonador).
Función del Harness: El arnés no solo edita archivos; usa el protocolo MCP para llamar a la herramienta de búsqueda de Perplexity y obtener "hechos" externos antes de generar código.
2. Contexto (Context Engineering):
Archivo AGENTS.md: Definiremos que el proyecto usa TypeScript y que las especificaciones deben basarse estrictamente en la investigación previa del MCP.
Investigación Externa: Inyectaremos en la ventana de contexto el resultado de la búsqueda de Perplexity: "Search the latest RFC 5322 requirements for email validation including edge cases and internationalized domain names".

3. Prompt (Outcome-Oriented):
Fase de Planificación: /plan Investiga mediante Perplexity los casos de error más comunes según el estándar RFC 5322. Con esa info, redacta una spec en email_spec.md..
Criterios de Éxito: "El validador debe ser una función pura, sin dependencias externas, y debe pasar una suite de tests con al menos 20 casos límite detectados en la investigación".
Brechas y Riesgos
Sobrecarga de Regex: Las expresiones regulares que cubren el 100% de la RFC son ilegibles y difíciles de mantener.

Context Rot: Si la investigación de Perplexity devuelve demasiada documentación técnica irrelevante, degradará la precisión del modelo en la fase de implementación.
Dependencia de Red: El MCP requiere conexión activa; si el servidor de Perplexity falla, el agente volverá a su conocimiento estático, que puede estar desfasado.
Propuesta Operativa (Flujo OPSX)
Propose (Research-led): Pedir al agente que use Perplexity para encontrar los 5 casos de emails válidos que la mayoría de los desarrolladores creen que son inválidos.
Apply (Test-First): Generar primero el archivo email.test.ts con esos 5 casos "extraños".
Verify: Ejecutar los tests en el terminal integrado. El agente debe iterar hasta que la lógica maneje correctamente los comentarios entre paréntesis o las IPs en el dominio, si la RFC lo exige.

Siguientes Pasos
Configurar mcp.json en VS Code para habilitar el servidor de Perplexity.
Lanzar el comando de investigación: "Claude, usa Perplexity para extraer una tabla de caracteres permitidos en la parte local de un email según RFC 5322".
Inicializar OpenSpec para estructurar la validación de esta lógica crítica

Mcp.json
{
  "name": "Perplexity MCP",
  "description": "Configuración de MCP para usar el servidor de Perplexity como fuente de contexto en VS Code.",
  "server": {
    "url": "https://mcp.perplexity.ai",
    "apiKeyEnv": "PERPLEXITY_MCP_API_KEY",
    "timeoutSeconds": 60
  },
  "agent": {
    "default": "claude-code",
    "enabled": true
  },
  "tooling": {
    "search": {
      "enabled": true,
      "provider": "perplexity"
    }
  }
}

Aquí tienes una propuesta mínima, práctica y orientada a ejecución para VS Code/Cursor, más una plantilla de AGENTS.md y email_spec.md para validar emails con foco RFC 5322. La configuración sigue el patrón MCP de VS Code/Cursor y usa un esquema de servidor Perplexity compatible con instalación típica vía npx o configuración equivalente en Cursor.[github]
mcp.json mínimo
Opción A: VS Code en el workspace
Crea .vscode/mcp.json:
json
{
  "servers": {
    "perplexity": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@perplexity-ai/mcp-server"],
      "env": {
        "PERPLEXITY_API_KEY": "${input:perplexity-key}"
      }
    }
  },
  "inputs": [
    {
      "type": "promptString",
      "id": "perplexity-key",
      "description": "Perplexity API Key",
      "password": true
    }
  ]
}
Este formato encaja con la estructura de MCP en .vscode/mcp.json y con el uso de un servidor Perplexity basado en stdio.[lowcode]
Opción B: Cursor global o por proyecto
Crea .cursor/mcp.json en el proyecto, o ~/.cursor/mcp.json para todos los proyectos:
json
{
  "mcpServers": {
    "perplexity": {
      "command": "npx",
      "args": ["-y", "@perplexity-ai/mcp-server"],
      "env": {
        "PERPLEXITY_API_KEY": "TU_API_KEY"
      }
    }
  }
}
Cursor documenta que puede leer configuración MCP desde .cursor/mcp.json, tanto a nivel de proyecto como global.[docs.perplexity]
AGENTS.md plantilla
text
# AGENTS.md

## Objetivo
Este repositorio implementa un validador de emails orientado a estándares RFC 5322, con apoyo de investigación externa vía Perplexity MCP.

## Stack
- TypeScript.
- Tests primero.
- Funciones puras.
- Sin dependencias externas para la lógica de validación.

## Principios
- No usar regex simplistas como solución final.
- La implementación debe cubrir casos límite reales de RFC 5322.
- Validar solo lo necesario para producción: claridad, mantenibilidad y cobertura de casos extremos.
- Si hay duda entre interpretación amplia y estricta, documentar la decisión en `email_spec.md`.

## Flujo de trabajo
1. Investigar requisitos y edge cases con Perplexity MCP.
2. Redactar/actualizar `email_spec.md`.
3. Crear primero `email.test.ts`.
4. Implementar `email.ts`.
5. Ejecutar tests y corregir iterativamente.

## Criterios de aceptación
- La función debe ser pura.
- Debe pasar la suite completa de tests.
- Debe incluir al menos 20 casos límite.
- Debe documentar explícitamente qué partes de RFC 5322 se soportan y cuáles no.

## Restricciones
- No introducir librerías de validación de email de terceros.
- No mezclar validación sintáctica con normalización de negocio.
- No asumir que una dirección “parecida” a un email es válida.
La plantilla está pensada para evitar deriva del agente y forzar un flujo de investigación → especificación → pruebas → implementación.[datatracker.ietf]
email_spec.md plantilla
text
# Email Validation Spec

## Propósito
Definir la validación de direcciones de correo electrónico con base en RFC 5322, con restricciones prácticas para uso en producción.

## Alcance
La validación cubrirá:
- Parte local.
- Dominio.
- Direcciones con dominio literal IP.
- Casos límite comunes de RFC.
- Soporte opcional para internacionalización si se decide explícitamente.

## Reglas funcionales

### Longitud
- La dirección completa no debe exceder 254 caracteres.

### Parte local
- Se permiten caracteres ASCII válidos según RFC 5322.
- Se permiten puntos si no aparecen al inicio, al final ni consecutivos.
- Se soportan comillas cuando estén correctamente delimitadas.
- Se documentará si se admiten comentarios entre paréntesis.

### Dominio
- Debe contener etiquetas válidas separadas por puntos.
- Cada etiqueta debe respetar límites de longitud y formato DNS.
- Se aceptan dominios literales IP si se decide soportarlos.
- Se documentará si se valida TLD explícitamente.

### Internacionalización
- Si se habilita IDN, se debe definir si se acepta Unicode directamente o solo punycode.

## Casos expresamente válidos
- `simple@example.com`
- `user.name+tag@example.co.uk`
- `user@[192.168.2.1]`

## Casos expresamente inválidos
- `.user@example.com`
- `user..name@example.com`
- `user@example..com`
- `user@-example.com`

## Decisiones de implementación
- La función será pura.
- No dependerá de red ni de APIs externas en tiempo de ejecución.
- La investigación externa solo se usa para diseñar la spec y los tests.

## Criterios de prueba
- Al menos 20 casos límite.
- Cobertura de local-part, dominio, IP literal, longitudes y casos con comillas.
- Cada excepción o ambigüedad debe quedar documentada.
RFC 5322 es la referencia formal del formato de correo, así que conviene que la spec deje claro qué parte se adopta literalmente y qué parte se restringe por mantenibilidad.[datatracker.ietf]
Recomendación táctica
Para tu caso, yo usaría esta secuencia: primero crear mcp.json, después AGENTS.md, luego email_spec.md, y por último email.test.ts. Ese orden reduce retrabajo y evita que el agente implemente una validación “aproximada” antes de fijar el criterio de verdad.[github]
Siguiente decisión útil
Si quieres, en el siguiente paso te preparo la suite inicial de 20 tests para email.test.ts, ya separada en:
·	válidos,
·	inválidos,
·	casos RFC dudosos,
·	y casos con decisión explícita de negocio.




Log de commandos:

Voy a revisar la configuración actual y los archivos clave para implementar el validador RFC 5322.

Read [](file:///e%3A/lidr)

Read [](file:///e%3A/lidr/.qodo/workflows/email_spec.md)

Searched for files matching `**/AGENTS.md`, 1 match

Encontré la spec existente y el agente de QODO; ahora verifico si `mcp.json` existe realmente en la raíz.

Searched for files matching `**/mcp.json`, no matches

Buscando la configuración y la estructura del proyecto antes de crear el validador y los tests.

Read [](file:///e%3A/lidr/.qodo)

Read [](file:///e%3A/lidr/.qodo/agents)

Read [](file:///e%3A/lidr/.qodo/agents/agents.md)

Estoy explorando el repositorio para validar si ya existe configuración de TypeScript y dónde agregar los archivos.

Read [](file:///e%3A/lidr)

Searched for files matching `**/package.json`, no matches

Searched for files matching `**/*.ts`, no matches

Searched for files matching `**/tsconfig*.json`, no matches

He preparado la implementación base y la configuración; ahora creo los archivos principales en el proyecto.

Created [](file:///e%3A/lidr/mcp.json)

Created [](file:///e%3A/lidr/package.json)

Created [](file:///e%3A/lidr/tsconfig.json)

Created [](file:///e%3A/lidr/jest.config.js)

Created [](file:///e%3A/lidr/src/email.ts)

Continuando con la implementación, agrego la suite de tests con casos límites RFC específicos.

Created [](file:///e%3A/lidr/src/email.test.ts)

Confirmando la implementación mediante instalación de dependencias y ejecución de los tests.

Ran terminal command: npm install

Checked terminal output

La instalación está en curso; reviso si el comando ya finalizó o requiere acción adicional.

Checked terminal output

El comando sigue activo; voy a obtener el último estado del terminal.

Checked terminal output

Siguiendo en el terminal para confirmar si la instalación terminó o sigue en progreso.

Checked terminal output

El terminal está bloqueado en un spinner; lo detengo y vuelvo a ejecutar la instalación con salida más estable.

Killed terminal

Ran terminal command: npm install --loglevel=error
Completed with input: npm install --loglevel=error

Encontré un problema de versión en package.json; ajusto las dependencias antes de volver a ejecutar los tests.

Ran terminal command: npm view @types/jest version ; npm view jest version ; npm view ts-jest version ; npm view typescript version

Ajusto el package.json para usar versiones de Jest y tipos que existen en npm.

Replacing 8 lines with 8 lines in [](file:///e%3A/lidr/package.json)


```

```


Ran terminal command: npm install --loglevel=error
Completed with input: npm install --loglevel=error

Voy a identificar la versión disponible de `ts-jest` y ajustar el proyecto a una combinación compatible.

Ran terminal command: npm view ts-jest versions --json | Select-String -Pattern '"' -Context 0,0 | Select-Object -First 40

Verifico la compatibilidad exacta de `ts-jest` con las versiones de Jest y TypeScript disponibles.

Ran terminal command: npm view ts-jest@20.0.2 peerDependencies --json ; echo '---' ; npm view ts-jest@20.0.2 dependencies --json ; echo '---' ; npm view ts-jest@20.0.2 dist-tags --json

Verifico las versiones compatibles de `jest`, `ts-jest`, y `@types/jest` antes de ajustar package.json.

Ran terminal command: npm view jest@29.4.11 version ; echo '---' ; npm view ts-jest@29.4.11 peerDependencies --json ; echo '---' ; npm view @types/jest@29.5.5 version

Verifico versiones válidas de los paquetes npm para ajustar package.json correctamente.

Ran terminal command: npm view ts-jest version ; echo '---' ; npm view jest version ; echo '---' ; npm view @types/jest version ; echo '---' ; npm view typescript version

Ajusto las pruebas para reflejar el soporte de comentarios RFC y agrego tipos de Node para compilar `net`.

Replacing 41 lines with 42 lines in [](file:///e%3A/lidr/package.json), [](file:///e%3A/lidr/.qodo/workflows/email_spec.md), [](file:///e%3A/lidr/src/email.test.ts)


```

```

```

```

```

```


Ran terminal command: npm install --loglevel=error

Checked terminal output

Verifico el contenido actual de package.json para asegurar que las versiones se hayan actualizado correctamente antes de continuar.

Read [](file:///e%3A/lidr/package.json)

Voy a corregir la versión de `ts-jest` en package.json y reinstalar las dependencias.

Replacing 7 lines with 9 lines in [](file:///e%3A/lidr/package.json)


```

```


Ran terminal command: npm install --loglevel=error

Checked terminal output

Reviso el estado de instalación y el contenido de `node_modules` para confirmar el progreso.

Ran terminal command: if (Test-Path node_modules) { Write-Output 'node_modules exists' } else { Write-Output 'node_modules missing' } ; npm install --loglevel=error

Checked terminal output

Voy a inspeccionar `package-lock.json` y el estado de los archivos para entender por qué npm sigue usando versiones antiguas.

Read [](file:///e%3A/lidr)

Confirmo el estado actual de dependencias instaladas y el lockfile para asegurar compatibilidad.

Searched for files matching `**/package-lock.json`, no matches

Verifico si node_modules contiene las dependencias esperadas y si la instalación de npm se completó correctamente.

Ran terminal command: Get-ChildItem node_modules | Where-Object { $_.PSIsContainer } | Select-Object -First 40 | ForEach-Object Name

Reviso el registro npm y las versiones disponibles para detectar por qué la instalación falla.

Ran terminal command: npm config get registry ; npm view @types/jest@30.0.0 version ; npm view @types/jest@29.7.0 version ; echo '29.7 missing' ; npm view ts-jest@29.4.11 version ; npm view jest@30.4.2 version ; npm install --loglevel=error --dry-run

Obteniendo el detalle del diagnóstico npm para ajustar la configuración correctamente.

Read [](file:///c%3A/Users/admin/AppData/Roaming/Code/User/workspaceStorage/09469f800fdfd8073fb9c9329805caa9/GitHub.copilot-chat/chat-session-resources/70d2f2e9-3ac8-431e-8f08-e9f1e5eabfe5/call_3SCMADU5jGJXG4WIqi2Ix0SF__vscode-1781690738342/content.txt#1-1), lines 1 to 200

Corrijo package.json para asegurar que `ts-jest` sea la versión correcta y no se esté solicitando la versión inexistente.

Replacing 7 lines with 9 lines in [](file:///e%3A/lidr/package.json)


```

```
Voy a limpiar el `node_modules` parcial y reinstalar con la versión corregida de `ts-jest`.

Ran terminal command: if (Test-Path node_modules) { Remove-Item node_modules -Recurse -Force } ; npm install --loglevel=error

✅ Dependencias instaladas correctamente. Ahora ejecuto la suite de tests para verificar el validador.

Ran terminal command: npm test -- --runInBand
Completed with input: npm test -- --runInBand

Voy a ajustar la configuración de TypeScript para que reconozca los globals de Jest y Node.

Read [](file:///e%3A/lidr/tsconfig.json)

Read [](file:///e%3A/lidr/jest.config.js)

Ajusto la configuración de TypeScript para que reconozca los globals de Jest y Node en los tests.

Replacing 13 lines with 15 lines in [](file:///e%3A/lidr/tsconfig.json)


```

```


Ran terminal command: npm test -- --runInBand

## ✅ Implementación completada

He creado y verificado:

- mcp.json con configuración de Perplexity MCP
- `AGENTS.md` ya existente y compatible con el flujo de RFC 5322
- email_spec.md actualizado con soporte de comentarios RFC, dominio literal e internacionalización
- email.ts con validador RFC 5322 puro
- email.test.ts con casos límite y validación de comentarios/IPs/TLDs
- package.json, tsconfig.json, jest.config.js

## 📌 Resultado de pruebas

- `npm test -- --runInBand`
- Tests: `7 passed`

## ⚠️ Nota importante

`ts-jest` emitió una advertencia de configuración deprecada sobre `globals`, pero la suite se ejecutó correctamente. Si deseas, puedo actualizar jest.config.js para usar la sintaxis recomendada `transform:`.

Made changes.

















Ejercicio 1. Parte B: Instalacion Openspec en sandbox










Paso 4: Exploracion guiada










OpenSpec/OPSX: el ciclo central es propose → apply → archive, con variantes recientes que añaden explore, sync, verify y comandos como /opsx:propose, /opsx:apply y /opsx:archive. En la versión reciente, el cambio se crea en openspec/changes/..., se implementa siguiendo la spec y luego se archiva moviendo el delta a openspec/changes/archive/ y sincronizando la spec principal.
Qué significa cada fase
·	Propose: defines la intención del cambio y OpenSpec genera la carpeta de cambio con spec, diseño y tareas.
·	Apply: el agente implementa paso a paso siguiendo la checklist de tareas.
·	Archive: cuando el cambio está listo, se fusiona el delta de especificación en la fuente de verdad y el cambio se mueve al archivo histórico.
Vocabulario útil
El vocabulario que te conviene retener para trabajar con OpenSpec es este: change, spec, tasks, archive, source of truth, verify y, según el perfil, explore/sync/continue/fast forward. En los materiales recientes, el flujo “core” suele ser propose → apply → sync → archive, mientras que el modo expandido incorpora verify antes de archivar.
Implicación práctica
Para tu caso, el enfoque correcto es: primero crear una propuesta clara con propose, luego hacer que el agente implemente y verifique contra la spec, y finalmente archivar cuando el resultado esté validado. Esto encaja muy bien con tu enfoque de validación RFC 5322 porque evita que el agente “invente” la solución y obliga a trabajar contra artefactos explícitos.
Recomendación operativa
Si estás en una versión reciente de OpenSpec, yo usaría esta secuencia mental:
1.	/opsx:propose para generar la carpeta del cambio.
2.	Revisar/ajustar la spec y los tests.
3.	/opsx:apply para implementar.
4.	/opsx:verify si tu perfil lo soporta.
5.	/opsx:archive para cerrar y mover el delta a histórico.
He captado el lenguaje operativo suficiente para seguir trabajando sobre tu validador de emails con un flujo spec-driven.



PASO 5. Registra 3 observaciones
 3 observaciones sencillas, pero útiles:
1.	Me llamó la atención que OpenSpec separa claramente specs/ como fuente de verdad y changes/ como espacio temporal de propuestas. Esa división hace que el historial de intención no se mezcle con el estado actual del sistema.
2.	No esperaba que el archivo de cambio se archivara moviéndolo a changes/archive/ en lugar de borrarlo. Es una decisión de diseño bastante buena porque conserva trazabilidad y auditoría sin perder el contexto del cambio.
3.	El cambio de comandos a /opsx:* me pareció más significativo de lo que parece. Da la sensación de que la herramienta pasó de ser un “asistente de specs” a un flujo de trabajo más operativo, con comandos orientados a acción y verificación.
