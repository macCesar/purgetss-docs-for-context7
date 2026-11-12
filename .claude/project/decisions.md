# Decisiones

## Reglas heredadas, registradas el 2026-11-11

- Se mantienen las reglas de `.claude/memory/repo-generado-no-editar.md` y `.claude/memory/sin-tags-ni-releases.md`, documentadas desde 2026-08-05: corregir en el origen y regenerar el espejo; nunca crear tags ni releases aquí. Esos archivos conservan el contexto histórico de las correcciones.

## 2026-11-11 — Ubicación de las notas de sesión

Usar `.claude/project/` para `requirements.md`, `context.md`, `decisions.md` y `status.md`, en lugar de `docs/project/`. El generador borra `docs/` entero antes de copiarlo; guardar allí notas propias las destruiría en la siguiente sincronización y mezclaría documentación interna con el contenido del producto.

`CLAUDE.md` importa sólo los tres documentos estables. `status.md` se consulta al retomar trabajo, sin importarlo al inicio. Las memorias existentes siguen siendo las reglas originales del espejo, no otro registro de sesión.
