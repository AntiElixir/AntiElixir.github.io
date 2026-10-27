# Yanjie Huang Personal Website

An English-first personal garden built with Jekyll for GitHub Pages.

## Local preview

```sh
bundle install
bundle exec jekyll serve
```

Open `http://127.0.0.1:4000`.

If local Ruby/Jekyll dependencies are not available on your machine, use the
fallback static preview:

```sh
python3 scripts/build_preview.py
cd _site
python3 -m http.server 4000
```

## Content

- Research and projects live in `_works/`.
- Published blog posts live in `_posts/`.
- Blog drafts live in `_drafts/`.
- Site-ready shared images live in `assets/shared/`.
- Site-ready project media live in `assets/works/<project>/`.
- Site-ready blog media live in `assets/posts/<post-slug>/`.
- Pixie media lives in `assets/cat/pixie/`.
- Raw materials that are not ready for the site live in `materials/`.
- See `MATERIALS_GUIDE.md` for the full folder map.
