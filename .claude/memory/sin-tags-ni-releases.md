# Nunca crear tags de git ni releases de GitHub en este repo

**Regla**: en `purgetss-docs-context7` **NO** se crean tags de git ni releases de GitHub. Nunca. Ni siquiera cuando se pida un "release" del repo.

**Por qué**: este repo no es software versionado, es un **espejo de documentación para que Context7 la indexe**. No tiene `package.json`, no se publica en npm y nadie instala nada desde aquí. El versionado real vive en el repo de PurgeTSS; las versiones que aparecen en `docs/changelog.md` describen al CLI, no a este repo. Un tag aquí no marca nada que exista.

**Cómo aplicar**: un "release" de este repo significa exactamente esto:

1. Agrupar los cambios en commits semánticos.
2. `git push origin main`.
3. Nada más. Sin `git tag`, sin `gh release create`.

**Error cometido (2026-08-05)**: al correr `/release` creé el tag `v7.11.1` con su GitHub release, y después `v7.12.1` con otro. César pidió borrar ambos y dejar constancia de la preferencia. Ninguno de los dos aportaba nada: la versión la dicta PurgeTSS.

Ver también [[repo-generado-no-editar]], porque el contenido de este repo tampoco se edita a mano.
