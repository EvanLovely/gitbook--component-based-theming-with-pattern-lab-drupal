## Website Ingredients {#intro}

* **HTML** \(Includes images.\)
* **CSS** \(Includes fonts, and also images.\)
* **JS** \(Could contain everything, but let's just sidestep that tangent.\)

CMS integration is made possible by shared assets. We want to avoid copying assets whenever possible because what we're shooting for is building a system that is as consistent as possible with it's assets between Pattern Lab and Drupal.

## Two levels of Integrated Pattern Labs

1. Shared CSS & JS
2. Shared CSS, JS, & HTML

## Shared CSS & JS is awesome and a great place to start {#css-js}

![](/assets/shared-assets-folder-structure.png)

Usually simply just pointing to the same CSS & JS files, with enough `../`. Pattern Lab's public directory keeps all compiled pages two levels deep, so you need to start with `../../` before you're at your public directory. If you had the below folder structure:

* theme\_name/
  * css/style.css
  * pattern-lab/
    * public/

You'd need to have this in your Pattern Lab header:

```html
<link href="../../../../css/style.css" rel="stylesheet">
```

However, Shared CSS, JS, & HTML is the  holyGrail[^1] so let's talk about how we can integrate Twig.

---

### Pattern Lab 1

* Single templating engines: Mustache.

### Pattern Lab 2

* Multiple templating engines, most notably **Twig**, the new templating engine in Drupal 8!

---

# Shared CSS, JS, and Twig {#css-js-twig}

## Crucial Concept: Template Paths

> How does the system find these files?[^6]

```twig
{% include 'path/to/my/template.twig' %}
{% include 'path-template' %}
{% include '@path/my/template.twig' %}
```

![](/assets/file-structure.png)

### Pattern Lab include:

Longhand:

```twig
{% include '02-molecules/cards/card.twig' %}
```

Shorthand:

```twig
{% include 'molecules-card' %}
```

This is awesome for just PL, but **must** be avoided for shared templates as Drupal does not have this.

### Drupal include:

```twig
{% include 'themes/dashing/pattern-lab/source/_patterns/02-molecules/cards/card.twig' %}
```

# Goal: Same path working in both places

## So, where's the base?

All Twig environments declare a _default_ base path:

* Pattern Lab: `pattern-lab/source/_patterns/`[^7]
* Drupal: web root

# Twig Namespaces to the Rescue!

Namespaces are a variable for a base path.

```
@foo === 'wherever/the/hell/foo/is/'
```

So we could then do:

```twig
{% include '@foo/my/file.twig' %}
```

Which is the same as:

```twig
{% include 'wherever/the/hell/foo/is/my/file.twig' %}
```

## Need to register same namespaces in Drupal and PL

`@molecules` must resolve to same base path in both systems.

Pattern Lab registers each folder[^8] below `pattern-lab/source/_patterns/` as a namespace, stripped of number prefixes.

* `@atoms === 'pattern-lab/source/_patterns/01-atoms/'`
* `@molecules === 'pattern-lab/source/_patterns/02-molecules/'`

Use the [Drupal module Component Libraries](https://www.drupal.org/project/components)[^9] to register the same namespaces

In `theme-name.info.yml`:

```yaml
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

## Now, your templates and include paths work in both systems

```twig
{% include '@molecules/path/to/template.twig' %}
```

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

[^1]: _Disclaimer_: May not provide happiness, eternal youth nor food in infinite abundance.

[^6]: Also applies to `extends`, `embed`, and other Twig tags.

[^7]: May be elsewhere, it's the value of `sourceDir` in config, then in the `_patterns/` sub-directory.

[^8]: Works with any folders names, you don't have to use Atomic Design's atoms, molecules, or organisms.
