# UI Components

For comprehensive component guidelines, refer to:

- **Views & Templates**: @docs/claude/views-and-templates.md
- **JavaScript/TypeScript**: @docs/claude/javascript-typescript.md

## Component Patterns

- **ViewComponent**: Use ViewComponent for reusable UI elements
- **Stimulus Controllers**: Use Stimulus for JavaScript interactivity with `data-controller` attributes
- **Slim Templates**: Server-side rendering with Slim template engine
- **Bootstrap 5**: Use Bootstrap 5 components and utilities for consistent UI

## Best Practices

- **Single Responsibility**: Each component should have one clear purpose
- **Stimulus Targets**: Use `data-{controller}-target` for DOM element references
- **TypeScript**: All Stimulus controllers should be written in TypeScript
