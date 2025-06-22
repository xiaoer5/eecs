## MkSlides 简介

使用 `MkSlides` 工具，基于 [Reveal.js](https://revealjs.com/) 强大功能，轻松地将 markdown 文件转换为漂亮的幻灯片。

`MkSlides` 是一个静态站点生成器，用于构建幻灯片。

幻灯片源文件是用 Markdown 编写的，并使用单个 YAML 配置文件进行配置。

工作流和命令很大程度上受到了 [MkDocs](https://pypi.org/project/mkdocs/) 和 [reveal-md](https://github.com/webpro/reveal-md) 的启发。


## MkSlides 安装

```bash
pip install mkslides
```

## MkSlides 预览

```bash
mkslides serve docs/
```

或者

```bash
mkslides serve test.md
```

## MkSlides 创建静态为网页

```bash
mkslides build docs/
```

或者

```bash
mkslides build test.md
```


## 参考

- [MkSlides](https://github.com/MartenBE/mkslides)
- [hogent-markdown-slides](https://github.com/HoGentTIN/hogent-markdown-slides)