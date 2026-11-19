# TanStack Query

[← Back: Frontend](./README.md)

Meta

[TanStack Query Docs](https://tanstack.com/query)

## What is _TanStack Query_?

### Desciption

makes fetching, caching, synchronizing and updating server state in your web applications a breeze.

Caching, request deduplication, retries, background refetching, window-focus updates,
and garbage collection arrive wired for real applications.

Model the resource, inputs, filters, and scope once so reads, writes, invalidation,
prefetching, and devtools all speak the same language.

Pending UI, optimistic updates, rollback, targeted invalidation, and background reconciliation
stay explicit instead of scattering through components.

### Origin

### Solves

## Core concepts

### Query

#### Cache

#### Lifecycle

The Lifecycle of a query:

1. Fetch: Resolve data while the _QueryClient_ controls cancellation, retries, and deduplication.

2. Propogate: Other users read the cache entry and aquire the data without another api request.

3. Data State: If data is stale then it stays visible while a background request is run to refresh it.

4. Garbage Collection: Unused data is discared after a breif window when the last observer leaves.

### Mutation

#### Lifecycle

1. optimistic write: `setQueryData(['todos'], next)`

2. server mutation: `await saveTodo(todo)`

3. targeted refresh: `invalidateQueries({ queryKey: ['todos'] })`

### Invalidate

## QueryKeys

### Hiarcical keys

### Invalidation

### Factory

## Queries

### SkipToken

### useSuspeseQuery

## Mutations

### Unpacking Mutation

### MutateAsync

## QueryClient

### Instanciate

### Usage
