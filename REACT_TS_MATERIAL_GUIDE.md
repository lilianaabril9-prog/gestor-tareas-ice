# Guía de Control para Desarrollo — React + TypeScript + Material UI

Propósito: guía práctica y de control para el desarrollo del MVP "Gestor de Tareas ICE" orientada a un curso de React. Basada en principios de Clean Code, minimiza dependencias y prioriza simplicidad.

## Resumen de decisiones arquitectónicas
- Stack: React 18 + TypeScript (strict) + Vite
- UI: Material UI (MUI) para componentes y diseño basado en Material
- Estado: `useState` + hooks custom; `Context` solo si es necesario (compartir estado global simple). Evitar Zustand/Redux para el curso.
- Persistencia: `localStorage` (servicio wrapper)
- IA: integrar un proveedor concreto (definir en README del proyecto) — usar un adaptador en `services/iceCalculator.ts` para facilitar cambio de proveedor.

## Objetivos de la guía
- Definir estructura de carpetas y convención de nombres
- Establecer división clara de componentes y responsabilidades
- Normas de TypeScript y Clean Code aplicadas a React
- Recomendaciones de estado y flujos asíncronos
- Reglas mínimas de linting y testing propuestas

---

## Estructura de carpetas recomendada

```
src/
├── assets/                # imágenes, fuentes, iconos
├── components/            # componentes UI reutilizables (atomos y moléculas)
│   ├── common/            # botones, inputs, badges, etc.
│   └── task/              # componentes específicos de dominio (TaskItem, TaskList)
├── features/              # características o páginas (feature-driven)
│   └── tasks/
│       ├── components/    # componentes propietarios de la feature
│       ├── hooks/         # hooks específicos de la feature
│       └── services/      # lógica y llamadas a APIs de la feature
├── hooks/                 # hooks globales reutilizables
├── services/              # adaptadores: storage, iceCalculator (IA), http
├── styles/                # tokens, variables, global css / theme
├── types/                 # tipos y interfaces compartidas
├── utils/                 # helpers puros, validación, parsing
├── App.tsx
├── main.tsx
└── index.css
```

Notas:
- Separar `components/common` (UI genérica) de `features/*` (lógica de negocio).
- `features` facilita escalabilidad y tests aislados.

---

## División en componentes y responsabilidades

Principio: seguir Atomic/Feature mix — componentes pequeños, con una única responsabilidad.

- `components/common/Button.tsx` — Wrapper estilizado sobre MUI `Button` para consistencia.
- `components/common/Input.tsx` — Input controlado, con soporte para errores y accesibilidad.
- `components/task/TaskItem.tsx` — Renderiza una tarea (props: `task`, callbacks: `onToggle`, `onEdit`, `onDelete`). No maneja side-effects.
- `components/task/TaskList.tsx` — Lista ordenada de `TaskItem`. Recibe lista ya ordenada.
- `features/tasks/components/TaskForm.tsx` — Form para crear tarea; llama a `useTasks` para submit y muestra estado de loading.
- `components/common/LoadingSpinner.tsx` — Indicador reutilizable.

Reglas:
- Cada componente en su carpeta con `index.ts` que exporte el componente.
- Componentes pequeños — si un componente supera ~200 líneas, dividirlo.
- No incluir lógica de negocio ni llamadas HTTP en componentes; colocarlas en `services` o `hooks`.

---

## Gestión del estado

Recomendación para MVP educativo y simple:

- Local: `useState` para inputs y estados UI.
- Lógica de tareas: encapsular en un hook `useTasks` ubicado en `features/tasks/hooks/useTasks.ts`.
  - `useTasks` expone API: `tasks`, `createTask(description)`, `updateTask(id, changes)`, `deleteTask(id)`, `toggleComplete(id)`.
  - Internamente usa `useReducer` o `useState` según conveniencia; `useReducer` recomendado si la lógica de mutaciones crece.
  - `useTasks` llama a `services/iceCalculator` para calcular I/C/E y a `services/storage` para persistencia en `localStorage`.
- Estado global: usar `React.Context` solo si dos o más features necesitan acceder al mismo estado (p.ej. settings del usuario). Mantenerlo mínimo.

Consideraciones asíncronas:
- Todas las llamadas a la API de IA deben tener timeout y manejo de errores centralizado.
- `useTasks` debe exponer estados de carga por operación (`creating`, `updating`, `error`) para mostrar UI apropiada.

---

## TypeScript - normas clave

- `tsconfig.json` con `strict: true`, `noImplicitAny: true`, `strictNullChecks: true`.
- Tipado explícito para props y hooks: preferir `interface` para props y `type` para uniones.
- Evitar `any`. Si es absolutamente necesario, comentar por qué y usar `unknown` antes de castear.
- Usar genéricos en hooks y utilidades cuando aplique.
- Definir tipos centrales en `types/` (por ejemplo `types/task.ts`):

```ts
export interface Task {
  id: string;
  description: string;
  completed: boolean;
  createdAt: string; // ISO
  ice?: {
    impact: number;
    confidence: number;
    ease: number;
    score: number;
  };
}
```

---

## Estilo de código y Clean Code aplicado a React

- Nombre de archivos: `PascalCase` para componentes (`TaskItem.tsx`), `camelCase` para hooks (`useTasks.ts`), `kebab-case` para assets.
- Export default: evitar `export default` en componentes; usar `export const TaskItem` y `export { TaskItem }` desde `index.ts` para facilitar refactors.
- Props: desestructurar en la firma solo cuando es claro; preferir `props: Props` para facilitar lectura en componentes complejos.
- Linting: ESLint con plugin `eslint-plugin-react`, `eslint-plugin-react-hooks`, `@typescript-eslint`.
- Formateo: Prettier (config simple) — establecer reglas comunes (singleQuote: true, trailingComma: 'es5').
- Comentarios: escribir comentarios solo para aclarar "por qué" y no "qué".
- Tests: nombrar con `.test.tsx` y colocar junto al componente.

Reglas de JSX/TSX:
- Mantener JSX limpio: extraer lógicas largas a variables/hook arriba del return.
- Evitar ternarios anidados; preferir funciones auxiliares.
- Usar short-circuit (&&) con cuidado, preferir condiciones claras.

---

## Material UI — pautas de uso

- Centralizar el tema en `styles/theme.ts` usando `createTheme` y `ThemeProvider` en `main.tsx`.
- No abusar del `sx` inline; preferir componentes custom que envuelvan MUI cuando se requiera estilo repetido.
- Mantener consistencia con tokens: colores, espaciados, tipografías.
- Componer componentes MUI: crear wrappers en `components/common` para variantes frecuentes (PrimaryButton, DangerIconButton).
- Evitar modificaciones profundas del DOM de MUI; preferir APIs públicas.

---

## Servicios y adaptadores

- `services/iceCalculator.ts` — adaptador para la API de IA. Exponer función `calculateICE(description): Promise<{impact,confidence,ease}>`.
  - Implementar validación de respuesta y normalización a rango [1,10].
  - Implementar fallback values en caso de error (ej. 5,5,5) y logging.
- `services/storage.ts` — wrapper de `localStorage` con serialización y límites (max 100 tareas).
- Mantener side-effects en `services` y hooks; los componentes llaman las funciones exportadas.

---

## Manejo de errores y UX

- Mostrar errores amigables y ofrecer acción (reintentar o editar manualmente la tarea).
- Timeouts: default 5s para llamadas externas.
- Estado de loading por operación; deshabilitar inputs relevantes durante la operación.

---

## Testing y calidad

- Base: Jest + React Testing Library (tests básicos: creación de tarea, ordenamiento ICE, fallback de IA).
- Pruebas de integración ligeras para el hook `useTasks` usando mocks para `iceCalculator`.
- Ejecutar ESLint y Prettier antes de commits (husky pre-commit) — opcional para curso simplificado.

---

## Dependencias mínimas recomendadas

- react, react-dom
- typescript
- vite
- @mui/material, @emotion/react, @emotion/styled
- axios (opcional) o usar fetch
- eslint, prettier, @typescript-eslint/eslint-plugin
- jest, @testing-library/react

Evitar añadir más librerías de gestión de estado o UI adicionales para mantener el foco educativo.

---

## Checklist de control (para revisión rápida)

- [ ] `tsconfig` en `strict` mode
- [ ] Estructura de carpetas respetada
- [ ] `useTasks` implementado y testado
- [ ] `services/iceCalculator` con validación y timeout
- [ ] `services/storage` con límite de 100 tareas
- [ ] Components atómicos y sin lógica de negocio
- [ ] Material Theme centralizado
- [ ] ESLint + Prettier configurados
- [ ] Tests básicos implementados

---

## Notas finales

Documento pensado para uso didáctico: prioriza claridad y reglas que ayuden a aprender buenas prácticas sin introducir capas de complejidad innecesarias.
