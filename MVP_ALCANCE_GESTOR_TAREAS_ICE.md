# Alcance Funcional - MVP Gestor de Tareas Inteligente con Modelo ICE

**Proyecto:** Gestor de Tareas Inteligente con Cálculo Automático de Prioridad ICE  
**Plataforma:** React (Frontend-only)  
**Complejidad:** Simple  
**Audiencia:** Curso de React  
**Fecha:** Mayo 2026

---

## 1. Descripción del Producto

Aplicación web que permite gestionar tareas con priorización automática utilizando el modelo ICE (Impact, Confidence, Ease). El sistema calcula automáticamente la puntuación ICE analizando la descripción de cada tarea mediante una API de IA gratuita, eliminando la necesidad de entrada manual de estos valores.

---

## 2. Objetivos del MVP

- ✅ Demostrar la integración de React con APIs externas
- ✅ Implementar lógica de gestión de estado simple
- ✅ Mostrar integración con APIs de IA gratuitas
- ✅ Crear un sistema de priorización automática
- ✅ Proporcionar una UX intuitiva y minimalista

---

## 3. Funcionalidades Incluidas

### 3.1 Gestión de Tareas Básica

- **Crear tarea:** Campo de entrada para descripción de la tarea
- **Listar tareas:** Visualización de todas las tareas en orden de prioridad ICE
- **Marcar como completada:** Checkbox para marcar tareas como done/no hecho
- **Eliminar tarea:** Botón para remover tareas de la lista
- **Editar tarea:** Permitir modificar la descripción de una tarea

Nota: Los detalles de los flujos (crear, editar, eliminar, completar) están descritos en la sección 6 "Flujo de Usuario". Mantener la sección 3.1 breve evita duplicación y facilita la lectura.

### 3.2 Cálculo Automático de ICE

- **Análisis mediante IA:** Al crear/editar una tarea, se envía la descripción a una API de IA gratuita
- **Cálculo de componentes:**
  - **Impact (Impacto):** Escala 1-10 - Cuán importante es resolver esta tarea
  - **Confidence (Confianza):** Escala 1-10 - Qué tan seguro estamos de que funcionará
  - **Ease (Facilidad):** Escala 1-10 - Qué tan fácil es implementar la solución
- **Score ICE:** Fórmula: `(Impact × Confidence × Ease) / 100`
- **Ordenamiento automático:** Las tareas se ordenan por score ICE descendente

### 3.3 Visualización de Información

- **Vista de lista:** Mostrar cada tarea con:
  - Descripción de la tarea
  - Valores de I, C, E desglosados
  - Score ICE total
  - Estado de completado (checkbox)
  - Botones de acciones (editar, eliminar)

- **Indicadores visuales:**
  - Color o badge basado en rango de ICE (alto, medio, bajo)
  - Indicador de carga mientras se calcula el ICE

### 3.4 Interacción con IA

- **API seleccionada:** Hugging Face API (gratuita) o similar
- **Prompt del modelo:** Solicitar al modelo que devuelva I, C, E en formato JSON
- **Manejo de errores:** Fallback con valores predeterminados si falla la API
- **Indicador de estado:** Mostrar cuando se está calculando la puntuación

### 3.5 Almacenamiento Temporal

- **localStorage:** Guardar tareas en localStorage del navegador (sesión)
- **Persistencia básica:** Las tareas se mantienen durante la sesión del usuario
- **Limpieza:** Botón para borrar todas las tareas

---

## 4. Funcionalidades Excluidas (Out of Scope)

- ❌ **Backend:** Sin servidor, sin base de datos centralizada
- ❌ **Autenticación:** Sin login, sin usuarios
- ❌ **Persistencia real:** Sin API de guardado permanente
- ❌ **Paginación:** Máximo ~50-100 tareas en la lista
- ❌ **Multiusuario:** Aplicación de usuario único (navegador local)
- ❌ **Tags/Categorías:** Sin clasificación por etiquetas
- ❌ **Notificaciones:** Sin sistema de alertas
- ❌ **Exportación/Importación:** Sin opción de backup
- ❌ **Modo oscuro/Temas:** Sin personalización de estilos
- ❌ **Comentarios/Notas:** Solo descripción simple de tarea

---

## 5. Modelo de Datos

```javascript
{
  id: "uuid-string",
  description: "Descripción de la tarea",
  completed: boolean,
  createdAt: "ISO-8601 timestamp",
  ice: {
    impact: number (1-10),
    confidence: number (1-10),
    ease: number (1-10),
    score: number (calculated)
  },
  status: "idle" | "loading" | "error"
}
```

---

## 6. Flujo de Usuario

### 6.1 Crear Nueva Tarea

1. Usuario ingresa descripción en campo de entrada
2. Usuario presiona botón "Crear" o Enter
3. Aplicación muestra indicador de carga
4. Se envía descripción a API de IA
5. API devuelve valores I, C, E
6. Tarea se añade a la lista ordenada por ICE
7. Campo de entrada se limpia

### 6.2 Completar Tarea

1. Usuario marca checkbox en la tarea
2. Tarea se marca visualmente como completada
3. No se elimina de la lista (opcional: mover al final)

### 6.3 Editar Tarea

1. Usuario presiona botón "Editar" en una tarea
2. Descripción se carga en el campo de entrada
3. Usuario modifica el texto
4. Usuario presiona "Guardar"
5. Se recalcula ICE con la nueva descripción
6. Lista se reordena si el score cambió

### 6.4 Eliminar Tarea

1. Usuario presiona botón "Eliminar"
2. Confirmación visual (tooltip o modal simple)
3. Tarea se remueve de la lista

---

## 7. Requisitos Técnicos

### 7.1 Frontend

- **Framework:** React 18+
- **Gestión de Estado:** useState, useContext o Zustand (simple)
- **Llamadas HTTP:** Fetch API o Axios
- **Estilos:** CSS modules, Tailwind CSS o styled-components
- **Versionado:** Git

### 7.2 API de IA

- **Opción 1:** Hugging Face Inference API (gratuita con límites)
- **Opción 2:** Groq API (gratuita con límites generosos)
- **Opción 3:** Replicate API (gratuita con créditos)

Ejemplo de formato de respuesta esperado:

```json
{
  "impact": 8,
  "confidence": 7,
  "ease": 5
}
```

### 7.3 Herramientas de Desarrollo

- **Build:** Vite o Create React App
- **Linting:** ESLint
- **Testing:** Opcional (Jest + React Testing Library)

---

## 8. Arquitectura Técnica

```
src/
├── components/
│   ├── TaskForm.jsx         # Formulario de entrada
│   ├── TaskList.jsx         # Lista de tareas
│   ├── TaskItem.jsx         # Componente individual de tarea
│   └── LoadingIndicator.jsx # Indicador de carga
├── services/
│   ├── iceCalculator.js     # Llamadas a API de IA
│   └── storage.js           # Gestión de localStorage
├── hooks/
│   └── useTasks.js          # Hook custom para gestión de tareas
├── styles/
│   └── App.css              # Estilos globales
├── App.jsx                  # Componente principal
└── main.jsx                 # Entry point
```

---

## 9. Criterios de Aceptación

- ✅ Usuario puede crear una tarea con descripción
- ✅ El sistema calcula automáticamente I, C, E mediante IA
- ✅ Las tareas se ordenan por score ICE
- ✅ Usuario puede marcar tareas como completadas
- ✅ Usuario puede editar descripción de tarea (recalcula ICE)
- ✅ Usuario puede eliminar tareas
- ✅ Las tareas persisten en localStorage durante la sesión
- ✅ Interfaz es responsiva y usable en desktop
- ✅ Manejo de errores cuando falla la API de IA
- ✅ Indicadores visuales claros del progreso de cálculo

---

## 10. Consideraciones de Aprendizaje

Este MVP es ideal para aprender:

- **Componentes funcionales de React:** Creación y composición
- **Hooks de React:** useState, useEffect, useContext
- **Llamadas asincrónicas:** Fetch API y manejo de promesas
- **Gestión de estado:** Props drilling vs Context API
- **Renderizado condicional:** Loading, error, success states
- **Eventos del usuario:** Click, submit, change handlers
- **localStorage API:** Persistencia básica en el navegador

---

## 11. Estimación de Esfuerzo

| Tarea | Horas | Complejidad |
|-------|-------|------------|
| Configuración del proyecto | 0.5 | Baja |
| Componentes básicos (Form, List, Item) | 2 | Baja |
| Integración con API de IA | 1.5 | Media |
| Lógica de gestión de tareas | 1.5 | Media |
| Estilos y diseño | 1 | Baja |
| Testing y debugging | 1.5 | Media |
| **TOTAL** | **~8 horas** | **Baja-Media** |

---

## 12. Próximos Pasos Futuros (No incluido en MVP)

- Agregar tags/categorías a tareas
- Backend con autenticación
- Persistencia en base de datos
- Compartir tareas con otros usuarios
- Recordatorios y notificaciones
- Integración con calendario
- Exportar tareas a PDF/CSV
- Análisis y reportes de productividad

---

**Fecha de creación:** 25 de Mayo, 2026  
**Versión del documento:** 1.0
