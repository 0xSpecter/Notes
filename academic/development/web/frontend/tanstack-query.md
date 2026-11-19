## TanStack Query

_TanStack Query_ is a query caching library, and its main purpose is to prevent
common issues when handling api calls.

> _TanStack Query_ was previously known as _React Query_ and was created by _Tanner Linsley_.
> It was renamed in v4 when it gained support for other frameworks, and is a part of _TanStack_,
> a family of open-source libraries for building web applications.

It works by creating a client that is used to manage calling api functions
and caching their results. It does a lot more like refetching, error handling,
request deduplication, etc, but you need only focus on the api function managing
and caching parts to make decent use of the library.

## QueryClient

### Instance

The main managing instance which does the heavy lifting and
controls everything.

It can simply be instantiated like this:

```ts
export const queryClient = new QueryClient();
```

But it also supports passing a good amount of options for configuring it,
here are some useful ones:

```ts
export const queryClient = new QueryClient({
    // Defines the default onError for queries
    queryCache: new QueryCache({
        onError: (error, query) => {
            if (error.response?.status === 404) {
                return;
            }

            console.error(`${query.meta?.errorMsg ?? "Failed to perform query"}`, error);
        },
    }),

    // Defines the default onError for mutations
    mutationCache: new MutationCache({
        onError: (error, _vars, _ctx, mutation) => {
            if (mutation.meta?.silent) return;

            console.error(`${mutation.meta?.errorMsg ?? "Failed to perform mutation"}`, error);
        },
    }),

    // Assigns default behaviour for hooks
    defaultOptions: {
        queries: {
            // How long data counts as fresh after a query.
            // Takes milliseconds, so 1000ms == 1s, * 30 to get 30s.
            staleTime: 1000 * 30,

            // How long unused data is kept in memory.
            // Takes milliseconds, so 1000ms == 1s, * 60 to get 1min, * 5 to get 5min.
            gcTime: 1000 * 60 * 5,

            // Whether to refetch on trigger event if data is stale.
            // They are all true by default.
            // refetchOnWindowFocus can be useful to set to false.
            refetchOnWindowFocus: false,
            refetchOnReconnect: true,
            refetchOnMount: true,

            // The retry option takes a function that returns a boolean.
            // true means it should retry the query and false means it's not tried again.
            // This function returns false if the error is 404 or if it has already been tried 2 times.
            retry: (failureCount, error) => {
                if (error.response?.status === 404) {
                    return false;
                }
                return failureCount < 2;
            },
        },
    },
});
```

Example usage of meta:

```ts
useQuery({
    queryKey: ["users"],
    queryFn: getUsers,
    meta: { errorMsg: "Failed to load documents" },
});
```

```ts
useMutation({
    mutationFn: someMutation,
    meta: { silent: true }, // If an error happens it's not logged
});
```

You should also include a TypeScript declare to define the type
of the errors your api client returns.

```ts
declare module "@tanstack/react-query" {
    interface Register {
        defaultError: AxiosError;
    }
}
```

#### staleTime

Stale data is by default refetched on these trigger events:

- The browser window is focused after having been unfocused
- When a component which runs that query is mounted.
- A network reconnect.

`staleTime` is the option you can set to manage when data goes stale.
It is by default set to **0ms**, which means data goes stale as soon as
it's fetched, so when a trigger event happens it's refetched.

If `staleTime` gets set to `1min` (`60000ms`), data then goes stale one
minute after being fetched.

It's advised to keep the default stale time low (10s -> 60s) as a too high one
might lead to data being out-of-date compared to the server but still considered fresh.

The best choice is to have a low default `staleTime`, with it being fine-tuned per query.
Some queries like notifications should have a `staleTime` of `0` while static data like
a document / pdf collection should have a high `staleTime`, like `10min` (`600 000ms`).

#### gcTime

How long cached data is stored in memory after the last component using it unmounts
is set by `gcTime` which stands for garbage collection time.
It is by default set to `5min` (`300 000ms`).

This timer starts counting down when the last component using it unmounts,
and is then canceled if a component using the data mounts,
the data is then fetched from memory and shown instantly.
If the timer runs out the data is discarded and when a component using
that data is mounted a new query is run.

If a component mounts and gets data from memory, it's shown instantly
but might have become stale. In this case it's queried in the background
and replaces the displayed data when it arrives.

`gcTime` should be at a minimum equal to `staleTime`, preferably higher.
If `gcTime` is lower than `staleTime`, then unused data will be
discarded while it's still considered fresh.
If a query sets a high `staleTime` it should also set a higher `gcTime`.

### Usage

You can access the queryClient in components with its related hook
`useQueryClient`. This is preferred over importing the original instance,
as it's managed by the hook's related provider.

```ts
const queryClient = useQueryClient()
```

To use the `useQueryClient` hook you need to share it with the rest
of the pages by wrapping the root `<Outlet />` with the `<QueryClientProvider>`
and give it your instance of the queryClient.

```ts
import { queryClient } from "./domain/queryClient";

export default function App() {
    return (
        <QueryClientProvider client={queryClient}>
            <Outlet />
        </QueryClientProvider>
    );
}
```

## QueryKeys

### What is a _queryKey_?

A queryKey is used to identify what data belongs to which query. Its similar to the key of a
key-value-store like a Hashmap or localStorage, where the value is the return value of the
api function. You can think of a queryKey like the key in `localStorage.setItem(key, value)`, where the
queryKey is the key and the value is what the last `queryFn` / api call returned.

Example:

```ts
const eventsQueryKey = ['events'] // <- QueryKey for getting all events
const usersQueryKey = ['users'] // <- QueryKey for getting all users
```

The reason for defining queryKeys as arrays is that a queryKey can consist of multiple
strings. This is to make it easier to relate and invalidate queries.

```ts
const upcommingEventsQueryKey = ['event', 'upcomming'] // <- QueryKey for getting upcomming events
const newAdminUsersQueryKey = ['users', 'new', 'admin'] // <- QueryKey for getting new admin users
```

### Invalidation

We preform invalidation of a queryKey when we mutate related data that might change what
the related query returns, aka saying that the current query is outdated.

it works kinda like this:

```ts
const eventsQueryKey = ['events']; // Defining a key

const events = getEvents(eventsQueryKey); // Fetching data and naming it ['events']

displayEvents(events) // Use fetched data

// Later ...

removeEvent(events[4]) // Remove the fifth event, getEvents now has outdated data, as it still contains the removed event.

invalidateQueries(eventsQueryKey) // Invalidate the query related to the given queryKey.
// This tell the client that the related query is outdated, prompting a refetch.
```

Say we have 3 queryKeys, in a tree like structure starting from the base key of `['events']`.

```ts
const eventsQueryKey = ['events']; // Defining a base key
const eventsUpcommingQueryKey = ['events', 'upcomming']; // The same as base but only upcomming
const eventsUpcommingFreeQueryKey = ['events', 'upcomming', 'free']; // The same as upcomming but only free events
```

You might think that to invalidate the related queries, you would have to
invalidate each one seperatly, like this:

```ts
invalidateQueries(eventsQueryKey) // Invalidates ['events']
invalidateQueries(eventsUpcommingQueryKey) // Invalidates ['events', 'upcomming']
invalidateQueries(eventsUpcommingFreeQueryKey) // Invalidates ['events', 'upcomming', 'free']
```

But queryKeys are actualy invalidated by prefix, meaning that invalidating `['events']`
also invalidates `['events', 'upcomming']` and `['events', 'upcomming', 'free']`.
This is why queryKeys are written like a tree and as an array.

```ts
invalidateQueries(eventsQueryKey) // Invalidates ['events'], ['events', 'upcomming'] and ['events', 'upcomming', 'free']
```

You don't just have to invalidate `['events']`, you can also invalidate lower prefixes.

```ts
invalidateQueries(eventsUpcommingQueryKey) // Invalidates ['events', 'upcomming'] and ['events', 'upcomming', 'free']
```

```ts
invalidateQueries(eventsUpcommingFreeQueryKey) // Invalidates ['events', 'upcomming', 'free']
```

Invalidating a key will only invalidate its children and not anything above it.
Invalidating `['user', 'events']` will only invalidate `['events']` and its children
not `['user']`. This is useful for creating dependant queries, where mutating user
might affect their events, but mutating events won't affect user.

### Dynamic queryKeys

QueryKeys don't have to be defined at compile time, only when the related query function is run.
This allows creating dynamic queryKeys, which is useful when, for example, fetching a spesific entry.

```ts
const userKey = (uid: number) => ['user', uid] // this creates a dynamic queryKey based on the users uid
```

Without dynamic queryKeys, getting 2 users using the same queryKey `['user']` would result in the second
query overwriting the first, or not running at all as the query is already marked as fresh from the first.

### Factory

A queryKey factory is a style of writing queryKeys popularised by xxx.
It keeps all keys in a single map and builds keys on top of its own keys,
spreading it to create a single new key.

All keys in a factory extend the all key, which then means that
invalidating the all key invalidates all the other keys in the map.
Building the keys on top of each other also prevents keys from drifting
or being accidantaly being changed.

```ts
export const userKeys = {
    all: ['users'] as const, // base key

    details: [...userKeys.all, 'details'] as const, // list users
    detail: (uid) => [...details, uid] as const, // get user by uid
}
```

You can create factories on top of other queryKey objects
to handle dependancies.

Lets say we have a table which's primary key is a uid,
it is then resonable to expect that it might change if users
is changed / invalidated. For example: if a user is deleted,
its relationship table is also deleted. This makes the current
relationship query out of date, so we need to invalidate it.

We could invalidate them seperatly

```ts
    queryClient.invalidateQueries({ queryKey: userKeys.all });
    queryClient.invalidateQueries({ queryKey: relationshipKeys.all });
```

But thats clunky and with 10 tables, it would be 10 lines, which
is not good code practice, its ugly and is painfull to write out.

We could collect our query keys in an array, then loop thought them.

```ts
[userKeys.all, relationshipKeys.all].map(key => {
    queryClient.invalidateQueries({ queryKey: key });
})
```

This is cleaner but still requires you to create and update
the array of keys and still starts getting ugly with more keys.

The best way to handle dependancies like this is by building
the dependant key on-top of its dependancies like this.

```ts
import { userKeys } from "~/domain/users/userKeys.ts"

export const relationshipKeys = {
    all: [...userKeys.all, 'relationships'] as const, // base dependant key

    details: [...relationshipKeys.all, 'details'] as const, // get entire table
    detail: (uid) => [...details, uid] as const, // get single entry with argument
}
```

Not only does this now only require us to invalidate _users_
to invalidate _relationships_, it can also be built in a tree style
where if _relationships_ also has a dependant table it can just build
itself on top of _relationships_ and it will still be invalidated by
invalidating _users_

> When importing keys from another module in domain
> be careful to import it from its respective `queryKeys`
> file and not `domain`.

## Queries

### useQuery

```ts
import { userKeys } from "./queryKeys"
import { getUsers } from "./api"

export function useGetUsers() {
    return useQuery({
        queryKey: userKeys.all,
        queryFn: getUsers(),
    });
}
```

### SkipToken

`skipToken` is a special value that when passing it as `queryFn` tells the query not to run.
Its a typescript workaround where allowes better handeling of when to fetch then enabled.

The problem with enabled is that condition and function are seperate
and typescript is to stupid too realize their connected.

The code under creates a typescript warning even thought it produces zero runetime errors.

```ts
export function useGetUser(uid: number | undefined) {
    return useQuery({
        queryKey: userKeys.all,
        queryFn: () => getUser(uid),
        enabled: uid !== undefined,
    });
}
```

With `skipToken`, the condition and the function are part of the same
expression, so typescript does not create a warning and is chill.
The function behaves the same but does not produce an error.

```ts
import { skipToken } from '@tanstack/react-query';

export function useGetUser(uid: number | undefined) {
    return useQuery({
        queryKey: userKeys.all,
        queryFn: uid !== undefined ? () => getUser(uid) : skipToken,
    });
}
```

## Mutations

### useMutation

```ts
import { userKeys } from "./queryKeys"
import { createUser } from "./api"

export function useCreateUser() {
    const queryClient = useQueryClient();

    return useMutation({
        mutationFn: (uid) => createUser(uid),
        onSuccess: () => {
            queryClient.invalidateQueries({ queryKey: userKeys.all });
        },
    });
}
```

```ts
const uid = 6769
```

```ts
const { mutate } = useCreateUser()
```

You can pass onSuccess, onError, etc as a argument to the mutate function in an options object
if you need custom actions taken.

```ts
mutate(uid, {
    onSuccess: data => {
        // :DD
    },
    onError: error => {
        // :((
    },
})
```

if you need to await data

```ts
const { mutateAsync } = useCreateUser()
// const { mutateAsync: mutate } = useCreateUser() // also possible to alias mutateAsync as just mutate
```

normal async function

```ts
async function submit(uid: string): User {
    const user = await mutateAsync(uid)
    return user
}
```

Promise _dot_ notation

```ts
const [user, setUser] = useState(null)

mutateAsync(uid).then(userData => setUser(userData));
```
