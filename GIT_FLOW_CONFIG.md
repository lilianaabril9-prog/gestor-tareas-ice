# Configuración Git Flow - Gestor de Tareas ICE

Estrategia de ramificación y protección de ramas para el proyecto.

---

## Flujo de Trabajo

### Ramas principales

- **`main`** — Rama de producción, protegida. Solo recibe merges desde PRs en `dev`.
- **`dev`** — Rama de desarrollo, donde convergen todas las features y donde se generan PRs desde las issues.
- **`feature/*`** — Ramas temporales para features específicas (opcional, dependerá de la estructura de issues).

### Flujo de commits

1. **Crear issue en GitHub** — Describe la tarea/feature a implementar.
2. **Crear rama desde `dev`** — Usar convención: `feature/nombre-del-feature` o `bugfix/descripcion`.
3. **Commits en la rama** — Trabajar localmente con commits descriptivos.
4. **Push a GitHub** — Enviar la rama al repositorio remoto.
5. **Crear PR contra `dev`** — La PR debe apuntar siempre a `dev`, no a `main`.
6. **Review y merge a `dev`** — Una vez aprobada, mergear a `dev`.
7. **Release a `main`** — Solo cuando `dev` está lista para producción, crear una PR de `dev` → `main` (requiere protección).

---

## Configuración de Protección de Ramas en GitHub

### Para la rama `main` (Protegida)

1. Ir a **Settings** del repositorio.
2. En el menú lateral, seleccionar **Branches**.
3. Bajo "Branch protection rules", hacer clic en **Add rule**.
4. Rellenar los campos:
   - **Branch name pattern:** `main`
   - **Require pull request reviews before merging:** ✓ activado (requerir al menos 1 review)
   - **Require status checks to pass before merging:** ✓ activado (si hay CI)
   - **Require branches to be up to date before merging:** ✓ activado
   - **Restrict who can push to matching branches:** ✓ activado (solo admins)
   - **Allow force pushes:** ✗ desactivado
   - **Allow deletions:** ✗ desactivado

5. Guardar la regla.

### Para la rama `dev` (Opcional, menos restrictiva)

1. Crear otra regla con:
   - **Branch name pattern:** `dev`
   - **Require pull request reviews before merging:** ✓ activado (1 review)
   - **Require status checks to pass:** ✓ (si aplica)
   - **Require branches to be up to date:** ✓ activado
   - **Allow force pushes:** ✗ desactivado
   - **Allow deletions:** ✗ desactivado

---

## Configuración Local (Git Hooks - Opcional)

Para automatizar validaciones locales antes de hacer push:

### 1. Crear archivo `.husky/pre-push` (si se usa Husky)

```bash
#!/bin/sh
# Validar que no se hace push directo a main o dev
BRANCH=$(git rev-parse --abbrev-ref HEAD)
if [[ "$BRANCH" == "main" || "$BRANCH" == "dev" ]]; then
  echo "❌ No puedes hacer push directo a $BRANCH. Usa PRs en su lugar."
  exit 1
fi
```

### 2. Instalación de Husky (opcional)

```bash
npm install husky --save-dev
npx husky install
npx husky add .husky/pre-push "bash .husky/pre-push"
```

---

## Convención de Nombres de Ramas

- **Features:** `feature/nombre-corto-en-kebab-case`
  - Ejemplo: `feature/crear-task-form`, `feature/api-ice-integration`

- **Bugfixes:** `bugfix/descripcion-del-bug`
  - Ejemplo: `bugfix/fix-iceCalculator-timeout`

- **Hotfixes (críticos en main):** `hotfix/descripcion`
  - Ejemplo: `hotfix/localStorage-serialization-error`

---

## Configuración de Commits

### Convención de mensajes de commits (Conventional Commits)

```
<tipo>(<scope>): <descripción breve>

<descripción detallada (opcional)>

<pie de página (opcional)>
```

Tipos recomendados:
- `feat:` — Nueva funcionalidad
- `fix:` — Corrección de bug
- `refactor:` — Cambio de código sin nuevo comportamiento
- `style:` — Cambios de formato (ESLint, Prettier)
- `test:` — Añadir o actualizar tests
- `docs:` — Cambios de documentación
- `chore:` — Cambios en configuración o dependencias

**Ejemplos:**
```
feat(tasks): implementar PriorityModal para confirmación de ICE
fix(iceCalculator): resolver timeout en llamada a API
docs(readme): actualizar instrucciones de setup
```

---

## Proceso Paso a Paso para una Feature

### 1. Crear la rama localmente

```bash
git checkout dev
git pull origin dev
git checkout -b feature/nombre-feature
```

### 2. Hacer commits con mensajes descriptivos

```bash
git add .
git commit -m "feat(components): crear TaskForm component con TextField"
git commit -m "feat(hooks): integrar useTasks con iceCalculator"
```

### 3. Hacer push a GitHub

```bash
git push -u origin feature/nombre-feature
```

### 4. Crear PR en GitHub

- Ir al repositorio en GitHub.
- Se mostrará un aviso para crear una PR.
- Asegurarse de que la PR apunta a `dev` (no a `main`).
- Rellenar título y descripción.
- Hacer clic en "Create pull request".

### 5. Review y merge

- Esperar a que otros revisen el código.
- Resolver comentarios si hay.
- Una vez aprobada, hacer clic en "Merge pull request".
- Seleccionar opción: "Squash and merge" (opcional, para mantener historia limpia).
- Borrar la rama remota tras el merge.

### 6. Actualizar rama local

```bash
git checkout dev
git pull origin dev
git branch -d feature/nombre-feature  # eliminar rama local
```

---

## Release a Production (main)

Solo cuando `dev` está estable y lista para producción:

### 1. Crear PR de `dev` → `main`

```bash
git checkout main
git pull origin main
git checkout -b release/v1.0.0  # rama temporal de release
git merge dev
git push -u origin release/v1.0.0
```

Luego crear la PR en GitHub desde `release/v1.0.0` → `main`.

### 2. Merge a `main` (requiere review y aprobación)

Esperar a que se apruebe la PR y hacer merge.

### 3. Volver a sincronizar `dev` (si hay cambios en main)

```bash
git checkout dev
git pull origin main
git push origin dev
```

---

## Checklist de Configuración

- [ ] Crear rama `dev` en GitHub
- [ ] Configurar regla de protección para `main`
- [ ] Configurar regla de protección para `dev` (opcional)
- [ ] Documentar convención de nombres de ramas en el README
- [ ] Instalar Husky para pre-push hooks (opcional)
- [ ] Crear template de PR (`.github/pull_request_template.md`)
- [ ] Establecer CODEOWNERS en `.github/CODEOWNERS` (si hay equipo)

---

## Referencias

- [GitHub Branch Protection](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git Flow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [Husky](https://typicode.github.io/husky/)
