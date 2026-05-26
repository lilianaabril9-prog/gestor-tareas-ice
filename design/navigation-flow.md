# Diagrama: Navegación de la app

Mermaid diagram simplificado de la navegación entre pantallas principales.

```mermaid
flowchart TD
  subgraph NAV[Navbar]
    Home[Home / TaskList]
    New[New Task]
    Filters[Filters]
    Settings[Settings]
  end
  Home --> TaskCard[TaskCard (abrir detalle)]
  TaskCard --> PriorityModal[PriorityModal (editar ICE)]
  New --> TaskForm[TaskForm Modal]
  Settings -->|open| SettingsPage[Settings Page]
  Filters -->|toggle| Home
  Home -->|click completed| CompletedList[Completed View]
  Home -->|search| SearchResults
```
