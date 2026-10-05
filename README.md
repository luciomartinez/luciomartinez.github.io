# Lucio Martinez

Personal website and writing archive for Lucio Martinez, built with Jekyll and
published as an open-source static site on GitHub Pages.

## Local development

The site uses the exact dependencies in `Gemfile.lock`. Docker is the easiest
way to run its older GitHub Pages toolchain without changing your system Ruby.

Install the locked dependencies into a reusable Docker volume:

```sh
docker run --rm \
  -e BUNDLE_FROZEN=true \
  -v "$PWD:/site" \
  -v luciomartinez-github-pages-bundle:/usr/local/bundle \
  -w /site \
  ruby:3.3 \
  bundle install --jobs 4 --retry 2
```

Build the complete site with the same GitHub Pages/Jekyll versions:

```sh
docker run --rm \
  -e BUNDLE_FROZEN=true \
  -v "$PWD:/site" \
  -v luciomartinez-github-pages-bundle:/usr/local/bundle \
  -w /site \
  ruby:3.3 \
  bundle exec jekyll build
```

Start the development server at `http://127.0.0.1:4000`. Jekyll rebuilds when
source files change, and LiveReload refreshes the browser automatically:

```sh
docker run --rm \
  -p 127.0.0.1:4000:4000 \
  -p 127.0.0.1:35729:35729 \
  -e BUNDLE_FROZEN=true \
  -v "$PWD:/site" \
  -v luciomartinez-github-pages-bundle:/usr/local/bundle \
  -w /site \
  ruby:3.3 \
  bundle exec jekyll serve --host 0.0.0.0 --livereload --force_polling
```

Polling lets Jekyll detect changes reliably through the Docker volume. Reload
an already-open browser tab once to connect it to LiveReload.

If a compatible Ruby and all bundled dependencies are installed directly on
your machine, the usual Jekyll development server also works:

```sh
bundle exec jekyll serve --livereload
```

## Publishing

The repository stays compatible with standard GitHub Pages: the Jekyll source
lives at the repository root, all pages are static, and no paid hosting service
or separate deployment platform is required. Review changes locally before
pushing them to the repository's configured GitHub Pages publishing branch.
