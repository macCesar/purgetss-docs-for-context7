# Requisitos del espejo

- Publicar una copia Markdown de la documentación de PurgeTSS para Context7. La fuente de verdad es `/Users/cesar/Developer/openSource/purgetss-docs`; este repositorio no contiene el CLI.
- Sincronizar contenido mediante `npm run clean:md` desde el repositorio fuente. Aceptación: `README.md` coincide con `docs/index.md` tras adaptar los enlaces a la raíz, y los enlaces nuevos resuelven.
- Conservar capacidades, requisitos y ejemplos vigentes. Los resúmenes de versiones anteriores pueden remitir al changelog completo si el contenido se conserva allí.
- Agrupar sincronizaciones en commits semánticos. No crear tags ni releases; las versiones documentadas pertenecen al CLI PurgeTSS.
- Mantener las notas fuera del árbol generado. El estado de sesión se lee bajo demanda, sin importarlo al inicio.
- El workflow `.github/workflows/context7-refresh.yml` solicita una actualización de Context7 al recibir cambios aplicables en `main`. La solicitud requiere el secreto `CONTEXT7_API_KEY`; la indexación final es asíncrona.
