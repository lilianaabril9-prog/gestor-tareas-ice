# Diagrama: Flujo de creación de tarea

Mermaid diagram del flujo desde que el usuario crea una tarea hasta que confirma el ICE sugerido.

```mermaid
flowchart TD
  A[Entrar a la app] --> B[Vista principal: TaskList]
  B --> C[Click "Nueva tarea"]
  C --> D[Modal: TaskForm (TextField)]
  D --> E[Usuario escribe descripción]
  E --> F[Enviar -> mostrar loading]
  F --> G[LLamada a API IA (iceCalculator)]
  G --> H{Respuesta válida?}
  H -- Sí --> I[Normalizar I,C,E -> calcular score ICE]
  H -- No --> J[Fallback values (5,5,5) y mostrar aviso]
  I --> K[Mostrar PriorityModal con valores sugeridos]
  J --> K
  K --> L{Usuario acepta?}
  L -- Sí --> M[Guardar tarea en localStorage y actualizar TaskList]
  L -- Edita valores --> N[Usuario modifica I/C/E manualmente]
  N --> M
  M --> O[Reordenar lista por score ICE]
  O --> P[Fin (Tarea confirmada y visible)]
```
