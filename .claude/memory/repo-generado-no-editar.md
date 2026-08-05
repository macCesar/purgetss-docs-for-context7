# `docs/` y `README.md` son generados: nunca editarlos aquí

**Regla**: `docs/` y `README.md` de este repo son **salida de un script**. Editarlos aquí es trabajo perdido: se sobrescriben en la siguiente sincronización.

**Quién los genera**: `npm run clean:md` desde `/Users/cesar/Developer/openSource/purgetss-docs` (el sitio Docusaurus, que es la fuente de verdad). El script:

- borra `docs/` **entero** (`fs.rmSync`) y lo vuelve a copiar desde el origen;
- regenera `README.md` a partir de `docs/index.md`, reescribiendo los enlaces relativos para que resuelvan desde la raíz.

**Cómo aplicar**: cualquier corrección de contenido, enlaces o formato se hace en `purgetss-docs` (los `.md` de `docs/` y `src/pages/`), o en el script `scripts/clean-md.mjs` de ese mismo repo si es un problema de traducción Docusaurus → GitHub. Después se corre `npm run clean:md` y los cambios aterrizan aquí.

**Único contenido propio de este repo**: `context7.json` y `.claude/`.

**Error cometido (2026-08-05)**: corregí 8 enlaces rotos por capitalización editando directamente `README.md` y varios `.md` de `docs/`. César preguntó por qué editaba archivos generados. Tuve que revertirlo todo y arreglarlo en el origen.

Ver también [[sin-tags-ni-releases]].
