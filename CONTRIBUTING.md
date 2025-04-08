# Guía de Contribución para GuiaElite-Protocolos-Defensa

¡Muchísimas gracias por tu interés en contribuir a la **Guía Elite de Protocolos para Defensa**! Valoramos enormemente el apoyo de la comunidad para hacer de esta guía la mejor referencia posible para Blue Teams, SOCs e Incident Responders.

Este documento establece unas pautas para asegurar que las contribuciones sean efectivas, consistentes y beneficiosas para todos.

## Código de Conducta

Esperamos que todos los participantes en este proyecto sigan nuestro [**Código de Conducta**](CODE_OF_CONDUCT.md). Al participar, te comprometes a respetar este código para mantener un ambiente colaborativo y respetuoso.
*(Nota: Es muy recomendable crear un archivo `CODE_OF_CONDUCT.md`. Puedes usar plantillas estándar que ofrece GitHub al crear el archivo).*

## ¿Cómo Puedo Contribuir?

¡Toda ayuda es bienvenida! Aquí tienes algunas ideas:

* **Reportando Errores:** ¿Ves información incorrecta, un enlace roto, un typo? ¡Infórmanos!
* **Sugiriendo Mejoras:** ¿Ideas para mejorar la estructura, el formato, o añadir secciones útiles?
* **Añadiendo Nuevos Protocolos:** ¿Falta algún protocolo crucial para defensa?
* **Mejorando Protocolos Existentes:** ¿Puedes añadir más detalles sobre riesgos, indicadores de monitorización más sutiles, pasos de remediación más efectivos o enlaces a MITRE ATT&CK más precisos?
* **Traducciones o Mejoras Lingüísticas:** Ayudar a que el lenguaje sea claro y preciso.

## Reportando Errores o Sugiriendo Mejoras (Issues)

La mejor forma de reportar errores o proponer ideas es a través de los **GitHub Issues**.

1.  **Busca Issues Existentes:** Antes de crear uno nuevo, por favor, revisa si ya existe un issue similar [aquí](https://github.com/Nervi0zz0/GuiaElite-Protocolos-Defensa/issues) (¡Asegúrate de que el enlace apunta a tu repositorio!).
2.  **Sé Claro y Detallado:**
    * Para **Errores:** Describe el problema, dónde lo encontraste (qué protocolo/sección), qué esperabas y qué encontraste. Si puedes, sugiere la corrección.
    * Para **Sugerencias:** Explica tu idea claramente, por qué crees que mejoraría la guía y cómo beneficiaría a los usuarios.
3.  **Usa Etiquetas:** Si es posible, usa etiquetas como `bug`, `enhancement`, `documentation`, `new protocol` para clasificar tu issue.

## Enviando Cambios Directamente (Pull Requests)

Si quieres corregir algo o añadir contenido tú mismo/a, ¡genial! Sigue estos pasos:

1.  **Haz un Fork:** Crea tu propia copia (fork) del repositorio `GuiaElite-Protocolos-Defensa` en tu cuenta de GitHub.
2.  **Clona tu Fork:** Descarga tu fork a tu máquina local: `git clone https://github.com/TU_USUARIO/GuiaElite-Protocolos-Defensa.git`
3.  **Crea una Rama:** Navega al directorio clonado y crea una rama nueva para tus cambios. Elige un nombre descriptivo:
    ```bash
    cd GuiaElite-Protocolos-Defensa
    git checkout -b mejora-protocolo-smb # O el nombre que prefieras
    ```
4.  **Realiza tus Cambios:** Edita los archivos `.md`. **¡Importante!** Intenta seguir la Guía de Estilo detallada abajo.
5.  **Haz Commit de tus Cambios:** Guarda tus cambios con mensajes claros que expliquen qué has hecho:
    ```bash
    git add .
    git commit -m "fix: Corregir enlace ATT&CK en SMB"
    # Ejemplos: feat: Añadir protocolo QUIC, docs: Mejorar descripción de riesgo en HTTP
    ```
6.  **Sube tu Rama:** Envía los cambios de tu rama local a tu fork en GitHub:
    ```bash
    git push origin mejora-protocolo-smb
    ```
7.  **Abre un Pull Request (PR):** Ve a tu fork en GitHub. Te aparecerá un botón para "Compare & pull request". Asegúrate de que la base sea el repositorio original (`Nervi0zz0/GuiaElite-Protocolos-Defensa` o el tuyo) y la rama `main`, y la comparación sea tu rama (`mejora-protocolo-smb`).
8.  **Describe tu PR:** Rellena la descripción del Pull Request explicando tus cambios de forma clara y concisa. Si tu PR soluciona un Issue existente, menciónalo usando `Closes #NUMERO_DEL_ISSUE`.
9.  **Revisión y Fusión:** Espera a que revisemos tu contribución. Podríamos pedirte algún ajuste o clarificación. Una vez aprobado, fusionaremos tu PR. ¡Gracias!

## Guía de Estilo (¡Importante!)

Para mantener la coherencia y la calidad "Elite" de esta guía:

* **Formato General:** Sigue la estructura de las entradas existentes (Encabezado H3, línea de info clave, Riesgos, Monitorización, Remediación).
* **Leyenda:** Respeta el uso de los iconos y el formato: `🔌 Puerto(s) | 🎯 Uso | 💀 Sev: ... | ⚠️ [[ID]](Link) Riesgo | 🔍 Monitorizar | 🛡️ Fix`.
* **Concisión y Claridad:** Información directa y accionable.
* **Markdown:** Usa `backticks` (\`) para `comandos`, `protocolos`, `puertos`, `EventIDs`, `nombres de fichero`, etc. Usa **negrita** para énfasis clave.
* **Enlaces:** Verifica que los enlaces (RFCs, ATT&CK, Docs) funcionen y sean relevantes. Usa el formato `[[ID Táctica/Técnica]](Link)`.
* **Severidad:** Usa las calaveras: 💀 (Bajo), 💀💀 (Medio), 💀💀💀 (Crítico).

## ¿Preguntas sobre Contribuciones?

Si tienes cualquier duda sobre el proceso de contribución, no dudes en abrir un **Issue** y plantear tu pregunta.

---

¡Agradecemos de antemano tu tiempo y esfuerzo para mejorar esta guía!
