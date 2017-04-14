The Twig [`include` tag](http://twig.sensiolabs.org/doc/2.x/tags/include.html) lets you pull one template into another, and it allows you to pass & transform data in the process using `with` in the tag like `{% raw %}{% include 'file.twig' with { item: true } %}{% endraw %}`. This can be used in the [`embed` tag](http://twig.sensiolabs.org/doc/2.x/tags/embed.html) or the [`extends` tag](http://twig.sensiolabs.org/doc/2.x/tags/extends.html) as well.

This gives us the opportunity to map the data; for example:

In `templates/block--system-branding-block.twig`:

```twig
{% include "@molecules/blocks/branding.twig" with {
    url: path('<front>'),
    text: site_name,
  }
%}
```

In `pattern-lab/source/_patterns/02-molecules/blocks/branding.twig`:

```twig
<a href="{{ url }}">
  {{ text }}
</a>
```
