# Cesar Augusto's website

A minimal professional website built with [Hugo](https://gohugo.io/). The current
version is intentionally small: a dependency-free landing page that can grow into
a portfolio and technical publishing site over time.

## Requirements

- Hugo 0.158.0 or newer

The site uses plain HTML and CSS, so Hugo Extended is not required.

## Local development

```sh
hugo server
```

The site is available at `http://localhost:1313/`.

## Build

```sh
hugo --minify
```

The generated site is written to `public/`.

## Structure

```text
.
|-- hugo.toml             # Site configuration and metadata
|-- layouts/home.html     # Home page template
`-- static/css/main.css   # Site styles
```

Professional facts and positioning are maintained in the sibling `../source/`
directory. Update that canonical source before introducing new public claims here.
