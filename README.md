# NanoUI

Repositorio contenedor de tres proyectos independientes (git submodules) del design system **NanoUI**:

| Submódulo | Qué es | Puerto |
| --- | --- | --- |
| `NanoUI-components` | Librería de componentes Angular 22 standalone, publicada como `@nanojjg/nano-ui-components` | — |
| `NanoUI-storybook` | Storybook 10 de la librería (sin app Angular) | 6006 |
| `NanoUI-web` | App demo Angular + Storybook | 4200 |

Cada submódulo es un proyecto npm separado con su propio `package.json`, `node_modules`, scripts y `AGENTS.md`. **No hay `package.json` ni comandos a nivel raíz.**

## Requisitos

- Node.js 22 y npm

## Instalación

No se instala en la raíz. Entra a cada submódulo e instala sus dependencias:

```bash
cd NanoUI-components && npm ci
cd NanoUI-storybook  && npm ci
cd NanoUI-web        && npm ci
```

## Comandos principales (dentro de cada submódulo)

| Tarea | NanoUI-components | NanoUI-storybook | NanoUI-web |
| --- | --- | --- | --- |
| Instalar | `npm ci` | `npm ci` | `npm ci` |
| Servidor dev | — | `npm run storybook` (6006) | `npm start` (4200) |
| Build | `npm run build` (ng-packagr → `./dist`) | `npm run build` (Storybook estático → `./dist`) | `npm run build` |
| Storybook | — | `npm run storybook` | `npm run storybook` |
| Test | no hay | no hay | `npm test` (`ng test`) |

Cada submódulo tiene su propio `AGENTS.md` con comandos exactos, convenciones y gotchas. Léelos antes de hacer cambios.

## Cómo se consume la librería

`NanoUI-storybook` y `NanoUI-web` consumen `@nanojjg/nano-ui-components` desde **npm**, no desde el código local de `NanoUI-components`. Siempre importa desde la raíz del paquete:

```ts
import { NanoButtonComponent } from '@nanojjg/nano-ui-components';
```

No existen imports profundos (`.../alert/nano-alert`) ni el scope `@nano/...`.

## Publicar una nueva versión de la librería

1. En `NanoUI-components`: `npm version patch --no-git-tag-version`
2. Commit `package.json` + `package-lock.json`
3. Crea el tag con prefijo `v` (debe coincidir con la versión de `package.json`, ej. `v0.2.6`) y empuja `main` + el tag
4. El workflow `.github/workflows/publish.yml` construye con ng-packagr y publica vía npm OIDC trusted publishing (sin `NPM_TOKEN`)
5. En `NanoUI-storybook` y `NanoUI-web`: sube la dependencia `@nanojjg/nano-ui-components` y ejecuta `npm install`

## Documentación por submódulo

- `NanoUI-components/AGENTS.md` — API pública, convenciones de componentes, `ng-package.json`, `tsconfig`, publicación
- `NanoUI-storybook/AGENTS.md` — comandos de Storybook, los tres ajustes que mantienen los metadatos de componentes, typecheck de historias
- `NanoUI-components/README.md` — pasos detallados de "Publishing a new version"
