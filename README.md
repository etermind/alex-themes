# Alex Themes

[![MIT License][license-image]][license-url]

[license-image]:http://img.shields.io/badge/license-MIT-000000.svg?style=flat-square
[license-url]:LICENSE

Welcome to Alex Themes! 

[Alex](https://github.com/etermind/alex) is a minimalist static site generator written in TypeScript.

By writing markdown files and changing a YAML config, you can create a static site in the matter of minutes.

## Themes repository

Here you'll find themes for Alex. Currently we provide:

- [minimal-rb](./minimal-rb/README.md): a minimal red/black theme for Alex. Great for a simple website.
- [apress](./apress/README.md): a theme to write documentation like VuePress.
- [astral](./astral/README.md): a modern theme with a simplified blogging section.

## How to setup a theme

1. Download the theme directory.
2. Use `alex init -t PATH_TO_THEME -o PATH_TO_SKELETON` to generate the skeleton of your website. You can look at demos to see a proper skeleton.
3. Write and add your content, change the config.yml file and then generate your static site using `alex generate -i PATH_TO_SKELETON -o PATH_TO_STATIC_WEBSITE`.
4. Upload your static website wherever you want!

## How to write a theme

Check out this [page](./THEMES.md) to learn everything about theme creation.
