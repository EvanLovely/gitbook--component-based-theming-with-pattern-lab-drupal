
## Website Ingredients

- HTML[^1]
- CSS[^2]
- JS[^3]

[^1]: Includes images.

[^2]: Includes fonts, and also images.

[^3]: Could contain everything, but let's just sidestep that tangent.

---

> CMS integration is made possible by shared[^4] assets.

## Two levels of Integrated Pattern Labs

1. Shared CSS & JS
2. Shared CSS, JS, & HTML

[^4]: `'shared' !== 'copied'`

---

## Shared CSS & JS is awesome and a great place to start

![](/assets/shared-assets-folder-structure.png)

---

## However

### `'Shared CSS, JS, & HTML' === holyGrail`[^5]

[^5]: Disclaimer: May not provide happiness, eternal youth nor food in infinite abundance.

---

## Pattern Lab 1

- Single templating engines: Mustache.

## Pattern Lab 2

- Multiple templating engines, most notably **Twig**, the new templating engine in Drupal 8!

---

# Crucial Concept: Template Paths

> How does the system find these files?[^6]

```twig
{% include 'path/to/my/template.twig' %}
{% include 'path-template' %}
{% include '@path/my/template.twig' %}
```

[^6]: Also applies to `{% extends %}`, `{% embed %}`, and other Twig tags.

---

## Pattern Lab Shorthand Syntax

![fit left](/assets/file-structure.png)

Longhand:

`{% include '02-molecules/cards/card.twig' %}`

Shorthand:

`{% include 'molecules-card' %}`

> This is awesome for just PL, but **must** be avoided for shared templates as Drupal does not have this.

---

### Pattern Lab include:

`{% include '02-molecules/cards/card.twig' %}`
or
`{% include 'molecules-card' %}`

### Drupal include:

`{% include 'themes/dashing/pattern-lab/source/_patterns/02-molecules/cards/card.twig' %}`

---

# Goal: Same path working in both places

## So, where's the base?

All Twig environments declare a *default* base path:

- Pattern Lab: `pattern-lab/source/_patterns/`[^7]
- Drupal: web root

[^7]: May be elsewhere, it's the value of `sourceDir` in config, then in the `_patterns/` sub-directory.

---

# Twig Namespaces to the Rescue!

Namespaces are a variable for a base path.

`@foo === 'wherever/the/hell/foo/is/'`

So we could then do:

```
{% include '@foo/my/file.twig' %}
```

Which is the same as:

```
{% include 'wherever/the/hell/foo/is/my/file.twig' %}
```

---

![fit left](/assets/file-structure.png)

## Need to register same namespaces in Drupal and PL

`@molecules` must resolve to same base path in both systems.

---

![fit left](/assets/file-structure.png)

Pattern Lab registers each folder[^8] below `pattern-lab/source/_patterns/` as one, stripped of numbers.

- `@atoms === 'pattern-lab/source/_patterns/01-atoms/'`
- `@molecules === 'pattern-lab/source/_patterns/02-molecules/'`

[^8]: Works with any folders names, you don't have to use Atomic Design's atoms, molecules, or organisms.

---

![fit left](/assets/file-structure.png)

Use the Drupal module Component Libraries[^9] register same namespaces

In `theme-name.info.yml`:

```yml
component-libraries:
  atoms:
    paths:
      - pattern-lab/source/_patterns/01-atoms
  molecules:
    paths:
      - pattern-lab/source/_patterns/02-molecules
  organisms:
    paths:
      - pattern-lab/source/_patterns/03-organisms
  templates:
    paths:
      - pattern-lab/source/_patterns/04-templates
  pages:
    paths:
      - pattern-lab/source/_patterns/05-pages
```

[^9]: https://www.drupal.org/project/components

---

## Now, your templates and include paths work in both systems

```twig
{% include '@molecules/path/to/template.twig' %}
```

---

In `block--system-branding-block.twig`:

```twig
{% include "@molecules/blocks/branding.twig"
  with {
    "url": path('<front>')
  }
%}
```

In `branding.twig`:

```twig
<div class="branding__site-logo">
  <a href="{{ url }}" title="{{ 'Home'|t }}" rel="home">
    <img src="{{ site_logo }}" alt="{{ 'Home'|t }}" />
  </a>
</div>
```
