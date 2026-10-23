# miqu.me

Source for [miqu.me](https://miqu.me), built with [Hugo](https://gohugo.io) and the [Terminal](https://github.com/panr/hugo-theme-terminal) theme.

## Layout

- `content/blog/`: posts, URL `/blog/:year/:month/:day/:slug/`
- `content/*.md`: the Who?, Games and Speaking pages
- `static/`: images and favicon
- `layouts/`: the RSS (`/index.xml`), Atom (`/atom.xml`) and `robots.txt` templates
- `_vendor/`: the theme, vendored so the build needs Hugo only

## Writing

Run `hugo server` and open http://localhost:1313. New post: `hugo new content blog/my-post.md`. Front matter is `title`, `date`, `slug` and `categories`. Put `<!--more-->` (no spaces) after the intro.

Keep posts theme-agnostic: standard front matter and Hugo's built-in shortcodes only. Theme settings live in `hugo.toml`.

## Updating the theme

Needs Go locally:

```sh
hugo mod get -u
hugo mod vendor
```

Commit `go.mod`, `go.sum` and `_vendor/`.
