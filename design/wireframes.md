# Wireframes y componentes (basado en MUI)

Wireframes simplificados representando las pantallas principales y componentes basados en Material UI.

```mermaid
flowchart TB
  subgraph Screen1[Home / TaskList]
    style Screen1 fill:#ffffff,stroke:#333,stroke-width:1px
    Navbar[Navbar: MUI AppBar with Title + NewTask Button]
    SearchBar[SearchBar: MUI TextField]
    TaskList[TaskList: MUI List -> TaskCard]
    Footer[Footer: actions (Clear all)]
  end
  subgraph TaskCardBox[TaskCard]
    Card[MUI Card]
    Checkbox[Checkbox]
    Description[Typograph y description]
    ICEChip[Chip badge: score]
    Actions[IconButton: edit, delete]
  end
  subgraph TaskFormModal[TaskForm Modal]
    Modal[Dialog]
    TextField[TextField: description]
    Submit[Button: Crear]
  end
  subgraph PriorityModalOverlay[PriorityModal]
    DialogP[Dialog]
    SuggestedICE[3 sliders or numeric inputs (Impact, Confidence, Ease)]
    ScoreDisplay[Score (calculated)]
    Confirm[Button: Confirmar]
    EditManual[Button: Editar manualmente]
  end
  Screen1 --> TaskCardBox
  Screen1 --> TaskFormModal
  TaskCardBox --> PriorityModalOverlay
```

Notas:
- Puedes exportar estos diagramas a PNG desde cualquier visor de Mermaid (VSCode Mermaid preview, mermaid-cli, o herramientas online).
- Si quieres que exporte las imágenes PNG directamente en el repo, puedo intentarlo, pero necesitaré generar las imágenes desde una herramienta que convierta Mermaid a PNG (p. ej. `mmdc`) —indícame si quieres que lo haga y lo ejecuto.
