# Contexto del proyecto

Este repositorio es el espejo Markdown de PurgeTSS para Context7. No tiene `package.json`, aplicación ejecutable ni suite propia. Las correcciones del producto y del sitio se hacen en sus repositorios fuente.

## Sincronización y publicación

- Fuente: `/Users/cesar/Developer/openSource/purgetss-docs`. Su comando `npm run clean:md` reemplaza `docs/` y genera `README.md` desde `docs/index.md`, adaptando enlaces relativos.
- Reglas originales: `.claude/memory/index.md` enlaza las restricciones sobre archivos generados y tags/releases. Consultarlas antes de modificar o publicar.
- `context7.json` identifica la biblioteca que indexa Context7.
- `.github/workflows/context7-refresh.yml` hace POST de actualización tras un push a `main` que cambie documentación/configuración aplicable, o por ejecución manual. Requiere `CONTEXT7_API_KEY`; pedir el refresh no acredita que la indexación haya terminado.
- El flujo SFTP de César sube archivos al guardarlos. El push Git y la ejecución de GitHub Actions son operaciones separadas; verificar sus resultados cuando se soliciten.

## Mapa de documentación

- `README.md`: entrada del espejo, navegación y resumen de versiones; se genera desde `docs/index.md`.
- `docs/app-assets/1-app-icons-and-branding.md`: App icons and branding; consultar al trabajar con este tema.
- `docs/app-assets/2-multi-density-images.md`: Multi-density images; consultar al trabajar con este tema.
- `docs/app-assets/3-svg-pipeline.md`: SVG-aware compile-time image pipeline; consultar al trabajar con este tema.
- `docs/best-practices/1-appearance-setup.md`: Appearance setup; consultar al trabajar con este tema.
- `docs/best-practices/2-semantic-colors.md`: Semantic colors; consultar al trabajar con este tema.
- `docs/best-practices/3-large-titles-on-ios.md`: Large titles on iOS; consultar al trabajar con este tema.
- `docs/best-practices/4-values-and-units.md`: Values and units; consultar al trabajar con este tema.
- `docs/changelog.md`: Changelog; consultar al trabajar con este tema.
- `docs/commands.md`: Commands; consultar al trabajar con este tema.
- `docs/customization/1-configuring-guide.md`: The `config` file; consultar al trabajar con este tema.
- `docs/customization/2-custom-rules.md`: Custom rules; consultar al trabajar con este tema.
- `docs/customization/3-the-apply-directive.md`: The `apply` directive; consultar al trabajar con este tema.
- `docs/customization/4-opacity.md`: The `opacity` modifier; consultar al trabajar con este tema.
- `docs/customization/5-arbitrary-values.md`: Arbitrary values; consultar al trabajar con este tema.
- `docs/customization/6-platform-and-device-modifiers.md`: Platform and device modifiers; consultar al trabajar con este tema.
- `docs/customization/7-custom-fonts.md`: Custom fonts; consultar al trabajar con este tema.
- `docs/customization/8-icon-fonts-libraries.md`: Icon font libraries; consultar al trabajar con este tema.
- `docs/grid-system.md`: Grid system; consultar al trabajar con este tema.
- `docs/index.md`: entrada del sitio y resumen de versiones; fuente del README generado.
- `docs/installation.md`: Installation; consultar al trabajar con este tema.
- `docs/performance.md`: Execution profiles and performance; consultar al trabajar con este tema.
- `docs/purgetss-ui/1-introduction.md`: Introduction; consultar al trabajar con este tema.
- `docs/purgetss-ui/10-appearance.md`: Appearance management; consultar al trabajar con este tema.
- `docs/purgetss-ui/2-the-play-method.md`: The `play` method; consultar al trabajar con este tema.
- `docs/purgetss-ui/2-titanium-classic.md`: Using `purgetss.ui` in Titanium Classic; consultar al trabajar con este tema.
- `docs/purgetss-ui/3-the-apply-method.md`: The `apply` method; consultar al trabajar con este tema.
- `docs/purgetss-ui/4-the-open-close-methods.md`: The `open` and `close` methods; consultar al trabajar con este tema.
- `docs/purgetss-ui/5-the-draggable-method.md`: The `draggable` method; consultar al trabajar con este tema.
- `docs/purgetss-ui/6-additional-methods.md`: Additional methods; consultar al trabajar con este tema.
- `docs/purgetss-ui/7-complex-ui-elements.md`: Complex UI elements; consultar al trabajar con este tema.
- `docs/purgetss-ui/8-available-utilities.md`: Alloy animation utilities; consultar al trabajar con este tema.
- `docs/purgetss-ui/9-implementation-rules.md`: Runtime implementation rules; consultar al trabajar con este tema.
- `docs/tikit.md`: Welcome to TiKit UI Components; consultar al trabajar con este tema.

## Validación proporcionada

Para sincronizaciones Markdown: revisar `git diff --check`, los enlaces y anclas añadidos, la equivalencia README/índice y las eliminaciones frente a la versión previa. No ejecutar pruebas del CLI desde este espejo ni atribuirle pruebas de sus repositorios fuente. Las afirmaciones de comportamiento publicadas no equivalen a una prueba de ejecución realizada aquí.

## Herramienta de trabajo

| Fecha | Asistente · modelo | Trabajo |
| --- | --- | --- |
| 2026-11-11 | Codex · GPT-6 | Revisión del diff recibido, commits semánticos e instalación de notas persistentes de sesión. |
