# zpalexander.com

The source for [zpalexander.com](https://zpalexander.com/), Zach Alexander's blog. It's a [Hugo](https://gohugo.io/) site: Hugo turns the Markdown in `content/` into static HTML at build time, and the result is hosted on GitHub Pages.

## Running it locally

You need [Git](https://git-scm.com/) and **Hugo extended 0.168.0** (the version the site is built and tested with; other versions may work but aren't tested). Go and Node aren't needed.

```bash
# Clone the repo, including the Ananke theme (a git submodule in themes/ananke)
git clone --recurse-submodules https://github.com/zpalexander/hugo-blog.git
cd hugo-blog

# Run the dev server with live reload
hugo server
```

Then open http://localhost:1313/.

- Already cloned without `--recurse-submodules`? Run `git submodule update --init --recursive` to fetch the theme.
- Add `-D` (`hugo server -D`) to also show draft posts.
- `hugo server` builds in development mode: pages get a `noindex` robots tag and the GoatCounter analytics script is left out, so local previews aren't counted.
- To build the production site into `public/` (what the deploy does): `hugo --gc --minify`.

## Deploying

Every push to `master` deploys automatically: the GitHub Actions workflow in `.github/workflows/hugo.yml` builds the site with Hugo 0.168.0 extended and publishes it to GitHub Pages, usually within a couple of minutes. A failed build leaves the live site unchanged. You can also redeploy without pushing from the workflow's page in the Actions tab (**Run workflow**). Dependabot keeps the workflow's pinned actions up to date.

## Adding a post

```bash
hugo new posts/name-of-post.md
```

This creates `content/posts/name-of-post.md` as a draft. Write the post in Markdown and set `draft: false` when it's ready to publish. It will live at `/posts/name-of-post/`.

## Editing a post

Open any Markdown file in `content/posts/` and start editing.

### Post front matter

* `title: ""`
* `date:` timestamp
* `draft:` true/false
* `featured_image: ""`: the banner image (e.g. `"/images/<post>/<file>.jpg"`); without one, the post gets the default snow banner
* `tags: []`
* `aliases: []`: old URLs that should redirect to this post (the posts carried over from the old Ghost site list their Ghost URLs here)

Images go in `static/images/` and are referenced with root-relative paths like `/images/...`.

## Where things are

* `content/`: posts (`content/posts/`), the About page (`content/about.md`) and the homepage subtitle (`content/_index.md`)
* `config.toml`: site settings, the nav menu, pagination and feeds
* `layouts/`: overrides of the Ananke theme's templates (the theme itself in `themes/ananke` is never edited)
* `assets/ananke/css/custom.css`: all custom styles; `syntax-monokai.css` is the generated code-highlighting stylesheet
* `static/`: images and the favicon, copied as-is into the site
