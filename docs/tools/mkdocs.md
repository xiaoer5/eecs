## Install mkdocs

```bash
pip install mkdocs

pip install mkdocs-material
```

## Create mkdocs project

```bash
mkdocs new .
```

the structure of the new mkdocs project directory as follows

```
.
├─ docs/
│  └─ index.md
└─ mkdocs.yml
```

## Configure mkdocs theme

configure the mkdocs theme (theme: material)

```bash
site_name: My site
site_url: https://mydomain.org/mysite
theme:
  name: material
```

## Add mkdocs navigation

```
site_name: My Docs
nav:
  - Home: index.md
  - Tools: tools.md
theme:
  name: material
```

In `docs` directory to create `index.md` and `tools.md` markdown files

```
.
├── docs
│   ├── index.md
│   └── tools.md
├── mkdocs.yml
```

## Deploy mkdocs project

The follow introduces how to deploy the mkdocs project to github page

Fisrt, Upload the whole mkdocs project to github repository, such as `git@github.com:test/xxxx.git`
And then， execute following command

```bash
mkdocs gh-deploy
```

Now, you can access the website: `https://test.github.io/xxx`



## Plugins

- [mkdocs-markmap](https://github.com/markmap/mkdocs_markmap)