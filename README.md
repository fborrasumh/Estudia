# EstudIA

Tu espacio de estudio con IA sobre tus propios apuntes. Aplicación web de un solo fichero (`index.html`), sin servidor ni cuentas.

**Usar la app:** https://fborrasumh.github.io/estudia/

**Idiomas:** español (por defecto), inglés y portugués; selector en la barra superior (o `?lang=en` / `?lang=pt` en la URL).

## Qué hace

- **Biblioteca de apuntes.** Lee Word (.docx), PDF con texto, PowerPoint (.pptx), texto y Markdown, o texto pegado. Divide cada documento en fragmentos y los indexa en el navegador (búsqueda BM25 por palabras, sin red).
- **Tutor con citas comprobadas.** Para cada pregunta, la app busca los fragmentos más relacionados y solo envía esos a la IA. La IA debe responder con citas literales; el código comprueba cada cita contra tu material: ✓ verificada, ⚠ no encontrada. Una respuesta sin ninguna cita verificada se marca «sin respaldo», y las cifras que no están en los fragmentos se señalan. Si la búsqueda no encuentra nada, no se llama a la IA.
- **Modo socrático.** El tutor guía con preguntas y dice qué fragmento releer, en lugar de dar la respuesta.
- **«Solo buscar en mis apuntes».** Muestra los fragmentos relevantes sin IA ni clave.
- **Tarjetas de huecos creadas por código (sin IA).** Al añadir un documento se eligen frases con términos que se repiten y se tapa uno. Funcionan sin clave y sin conexión; se aceptan tildes omitidas y una errata.
- **Preguntas tipo test de la IA, filtradas por código.** Solo entran en la práctica si tienen enunciado, cuatro opciones distintas, una respuesta válida, ninguna opción «todas/ninguna de las anteriores» y una cita que aparece literalmente en el material. Las rechazadas se guardan aparte con su motivo.
- **Pistas sin chivatazos.** Al pedir pista, la IA no recibe cuál es la opción correcta y, además, el código borra las frases que contienen la respuesta o señalan una opción. Sin clave, hay pista local.
- **Ruta de repaso explicable.** Repaso espaciado con cajas de Leitner (1, 2, 4, 8, 16 y 32 días; un fallo vuelve a los 10 minutos), modelo de dominio por concepto (logístico tipo Elo, con olvido de 0,08 por día) y elección determinista del siguiente ítem: primero lo que vence, después lo nuevo del concepto con menos dominio, intercalando conceptos. Cada ítem muestra **por qué** toca ahora.
- **Progreso y registro.** Dominio por concepto, calendario de repasos de 7 días y registro de estudio (respuestas y preguntas al tutor) que se puede descargar o borrar.
- **Descargas.** Guía de estudio en Word (dominio, ítems fallados con su cita, preguntas al tutor con citas verificadas), progreso en CSV (`;`, UTF-8 con BOM), preguntas en formato GIFT para Moodle y copia de seguridad JSON que se puede restaurar. Word, CSV y GIFT se generan en el navegador sin librerías externas.
- **Ejemplo sin clave** en los tres idiomas, con una pregunta de la «IA» que trae una cita inventada a propósito para ver cómo la rechaza el código.

## Cómo se usa la IA

Con la propia clave de OpenAI, Google Gemini o Anthropic Claude (botón «IA»; el desplegable muestra los modelos de tu clave). La clave se guarda solo en el navegador y cada persona paga su uso; basta un modelo económico. La IA propone; el código comprueba citas, cifras, forma de las preguntas y pistas.

## Privacidad

- Se guarda **solo en este navegador** (IndexedDB): los apuntes, la conversación, los ítems, el progreso y el registro de estudio. La clave, en `localStorage`.
- Sale hacia el proveedor de IA **solo** lo necesario en cada petición: los fragmentos recuperados (hasta 5) y la pregunta, o los fragmentos elegidos para crear preguntas, con correos, teléfonos y DNI/NIE enmascarados. Antes del primer envío de cada sesión se muestra una muestra de lo que sale y se pide confirmación.
- La copia JSON no incluye claves. No hay analítica, cookies de terceros ni servidor propio. La política de contenido (CSP) solo permite conectar con los tres proveedores de IA y, para leer Word/PDF/PowerPoint, con cdnjs/jsDelivr.

## Límites

- No lee imágenes ni PDF escaneados (sin OCR); en los PDF no se tienen en cuenta las imágenes.
- La búsqueda es léxica (BM25): no entiende sinónimos ni paráfrasis; si no encuentra tu tema, prueba otras palabras.
- Una cita verificada prueba **de dónde sale** el texto, no que la explicación de la IA sea correcta. El modo socrático depende de las instrucciones a la IA y puede fallar.
- El código comprueba la forma y la cita de las preguntas, no su calidad pedagógica; las tarjetas de huecos son mecánicas y algunas salen triviales. Bórralas desde el banco.
- El dominio es una **estimación** para orientar el estudio, no una nota. Los parámetros (K = 0,5; olvido 0,08/día; cajas de Leitner) son razonables pero no están calibrados con datos.
- La app aplica principios con evidencia (práctica de recuperación, espaciado, intercalado), pero **su efecto con esta herramienta no se ha medido**.
- Sin servidor: si se borran los datos del navegador se pierde el trabajo (usa la copia JSON). No hay sincronización entre dispositivos.
- Probada con IA simulada de los tres proveedores (incluidas respuestas que mienten), Chromium de escritorio y móvil; no probada con claves reales.

## Base pedagógica

- Roediger, H. L., y Karpicke, J. D. (2006). Test-enhanced learning. *Psychological Science*, 17(3), 249-255. https://doi.org/10.1111/j.1467-9280.2006.01693.x
- Cepeda, N. J., Pashler, H., Vul, E., Wixted, J. T., y Rohrer, D. (2006). Distributed practice in verbal recall tasks. *Psychological Bulletin*, 132(3), 354-380. https://doi.org/10.1037/0033-2909.132.3.354
- Rohrer, D., y Taylor, K. (2007). The shuffling of mathematics problems improves learning. *Instructional Science*, 35, 481-498. https://doi.org/10.1007/s11251-007-9015-8
- Dunlosky, J., Rawson, K. A., Marsh, E. J., Nathan, M. J., y Willingham, D. T. (2013). Improving students' learning with effective learning techniques. *Psychological Science in the Public Interest*, 14(1), 4-58. https://doi.org/10.1177/1529100612453266

## Pruebas realizadas

- Lógica pura en Node (`tests/nucleo_test.js`): búsqueda, verificación de citas, validación de preguntas, huecos, filtro de pistas, GIFT y determinismo de la ruta (mismos eventos → misma ruta).
- Navegador (`tests/smoke_test.py`, `tests/extra_test.py`, Playwright): 56 + 11 comprobaciones; CSP; ejemplo sin clave en tres idiomas; citas y cifras inventadas detectadas; preguntas mal formadas rechazadas; pistas que delatan la respuesta filtradas; enmascarado de datos personales; descargas (Word abierto con python-docx, también con CDN bloqueado); restaurar copia; persistencia; errores de IA traducidos; práctica sin conexión; lectura real de Word, PDF y PowerPoint; tutor con los tres proveedores; **axe-core sin infracciones WCAG 2.x A/AA** en 5 pantallas × 3 idiomas; uso con teclado; móvil de 390 px sin desborde; portada útil en 0,4 s con la CPU ralentizada ×4. HTML de unos 260 KB.

## Adoptarla en otra asignatura o universidad

No hace falta tocar el código: cada estudiante sube sus apuntes. Para una asignatura concreta, el profesorado puede repartir un fichero de copia JSON con los apuntes y las preguntas ya revisadas, que el estudiante restaura en «Progreso → Restaurar copia». Para traducir, edita `tests/traducciones.py` y ejecuta `python3 tests/traducciones.py && python3 build.py`.

## Origen de la idea

Inspirada en [DeepTutor](https://github.com/HKUDS/DeepTutor) (HKUDS, Universidad de Hong Kong; licencia Apache-2.0), un espacio de aprendizaje con agentes, bases de conocimiento y servidor Python. EstudIA reimplementa una parte de la idea (tutor fundamentado en el material del estudiante, preguntas, ruta de dominio y memoria inspeccionable) en un solo HTML que funciona en el navegador, **sin reutilizar su código**. No incluye búsqueda web, agentes externos, generación de libros, compañeros persistentes ni sincronización.

## Autoría

Fernando Borrás Rocher (Universidad Miguel Hernández de Elche). Roles CRediT: conceptualización, software, validación, redacción.

ORCID: Fernando Borrás Rocher [0000-0002-5519-4573](https://orcid.org/0000-0002-5519-4573)

## Cómo citar

Borrás Rocher, F. (2026). *EstudIA* (v1.0.0) [Software]. (DOI en trámite)

## Licencia

MIT. Véase [LICENSE](LICENSE).
