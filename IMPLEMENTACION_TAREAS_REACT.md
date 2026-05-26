# Tareas de Implementación para el Gestor de Tareas ICE

Este documento divide el proyecto en 7 tareas de implementación ordenadas, diseñadas para un desarrollo React + TypeScript + Material UI simple y educativo.

## Tarea 1 - Inicializar el proyecto y configuración básica
- Crear el proyecto con Vite + React + TypeScript.
- Configurar `tsconfig.json` con `strict: true`.
- Instalar dependencias mínimas: `react`, `react-dom`, `@mui/material`, `@emotion/react`, `@emotion/styled`.
- Configurar ESLint y Prettier con reglas básicas para React y TypeScript.
- Crear estructura inicial de carpetas: `src/components`, `src/features`, `src/services`, `src/hooks`, `src/types`, `src/styles`.

## Tarea 2 - Definir tipos y modelo de datos
- Crear `src/types/task.ts` con la interfaz `Task`.
- Definir tipo `IceValues` y `TaskStatus`.
- Asegurar tipado de las propiedades de los componentes y hooks.
- Crear constantes de configuración: `MAX_TASKS`, `ICE_FALLBACK_VALUES`, etc.

## Tarea 3 - Implementar almacenamiento local y servicios
- Crear `src/services/storage.ts` para guardar/cargar tareas desde `localStorage`.
- Añadir serialización segura y manejo de errores.
- Crear `src/services/iceCalculator.ts` con la función `calculateICE(description)`.
- Implementar validación de respuesta de la API y fallback en caso de error.

## Tarea 4 - Crear hook principal de gestión de tareas
- Implementar `src/features/tasks/hooks/useTasks.ts`.
- Exponer los métodos: `tasks`, `loading`, `error`, `createTask`, `updateTask`, `deleteTask`, `toggleComplete`.
- Integrar `storage` y `iceCalculator` dentro del hook.
- Ordenar tareas automáticamente por score ICE descendente.

## Tarea 5 - Construir la UI principal con Material UI
- Crear `src/components/common/Navbar.tsx` con MUI `AppBar`.
- Crear `src/features/tasks/components/TaskList.tsx` y `TaskCard.tsx` con MUI `List`, `Card`, `Checkbox`, `Chip`.
- Crear `src/features/tasks/components/TaskForm.tsx` como MUI `Dialog` con `TextField`.
- Añadir `LoadingSpinner` y mensajes de estado.

## Tarea 6 - Implementar el flujo de creación y confirmación ICE
- Implementar apertura del modal `TaskForm` desde el `Navbar` o botón principal.
- Al enviar descripción, activar el estado de carga y llamar a `calculateICE`.
- Mostrar un `PriorityModal` con valores sugeridos de Impact, Confidence y Ease.
- Permitir confirmar o ajustar manualmente los valores antes de guardar.

## Tarea 7 - UX final y ajustes de calidad
- Añadir validación de formulario (descripción no vacía, max length).
- Permitir eliminar tareas y marcar como completadas.
- Añadir un botón de limpiar todo y estado de confirmación simple.
- Ajustar estilos con tema MUI y `ThemeProvider`.
- Escribir pruebas básicas para `useTasks` y componentes clave.
- Revisar con ESLint/Prettier y corregir advertencias.
