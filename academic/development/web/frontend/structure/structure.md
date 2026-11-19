# Structure

[(← back)](./README.md)

This file will in future discuss my prefered way of structureing a web development project

| Folder                           | Holds                                  |
|----------------------------------|----------------------------------------|
| [`guides/`](guides/)             | How to add something new, step by step |
| [`systems/`](systems/)           | How a part of the base works inside    |
| [`integrations/`](integrations/) | Third-party services and libraries     |
| [`directories/`](directories/)   | One folder per `app/` folder           |
| [`templates/`](templates/)       | Starting points for new docs           |
| [`style/`](style/)               | Code style snippets to copy            |

## Index

### Guides

- [Components](guides/components.md): folder layout, styling prop, stories
- [Provider](guides/provider.md): sharing a value through context
- [Writing docs](guides/writing-docs.md): adding a doc, line length, style.

### Systems

- [Styling](systems/styling.md): options, mixins, tokens, Storybook controls

### Style

- [Headers](style/headers.md): section and subheader comments for `.scss`

### Directories

Mirrors `app/`. Each folder's README says what the `app/` folder holds and
lists its docs.

- [components](directories/components/README.md): one doc per component
- [domain](directories/domain/README.md): the data layer
- [hooks](directories/hooks/README.md)
- [i18n](directories/i18n/README.md)
- [layouts](directories/layouts/README.md)
- [providers](directories/providers/README.md)
- [routes](directories/routes/README.md)
- [stories](directories/stories/README.md)

### Integrations

- [Firebase](integrations/firebase.md)
- [Supabase](integrations/supabase.md)
- [TanStack Query](integrations/tanstack-query.md)

## Writing a doc

There a templates in [`templates/`](templates/) that can be used for
quick writing, they should also roughtly be followed for other docs.
Make sure that docs don't write lines longer then 80 characters.

For more above writing docs, check out [Writing docs](guides/writing-docs.md).
