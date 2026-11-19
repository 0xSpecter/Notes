# Sassy CSS - _SCSS_

**Also Known As:** _Syntactically Awesome Style Sheets_
is a great way to handle compicated styling eaily and cleanly.
If you learn the basic concepts and syntax its amazing what
is possible to do with relativly little code.

> `SCSS` and `SASS` are the same css preprossesor but differ
> in how they are written. `SCSS` more closely resembals `CSS`
> with bracket notation while `SASS` looks more like python or lua
> with its bracketless style. I would recommend sticking to `SCSS`
> as brackets are generally a good thing for easy of readablity.
> Though their syntax outside of that is essentialy the same.
> I will be refering to both just as _SCSS_ as that is my
> preffered variant, but everything here still applies to _SASS_.

_SCSS_ is just a preprossesor, that means that it does not change
the language at runtime, its still _CSS_ which is running on the website.
Its the same concept as _React_ and other web technoloyes, they make
writing the code easier, but still compile to html and js.

## idk overcategories yet

### Constants

A constant in `SCSS` is defined by the `$` sign followed by
the constant name, `:` some value, then `;` to close of, like
standard `css` properties. spaces are not allowed for obius reasons.

_SCSS_ variables are preprossessed meaning their turned into
static values when compiling, so unlike _CSS_ variables, they
cannot be changed at runtime. You can mutate them during compelation
but that has very few use cases, _shadowing_ is the only normaly used
case, but its hard to really call that a mutation.

#### Example

```scss
$primary: #00ff99;              // Works

$background: black;             // Works

$pixels: calc(20% + 50px);      // Works

$primary-copy: $primary;        // Works
```

`SCSS` treats hyphens and underscores as identical for most
purposes, so `$color-primary` and `$color_primary`
refere to the same constant.

---

### Mixins

...

---

### Lists

...

---
