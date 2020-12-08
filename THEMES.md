# Writing themes

If you would like to write a custom theme for Alex, it is possible and easy.

All you need is to follow those guidelines. In 10 minutes, you'll have your structure and you can start customizing the HTML pages and CSS files. 

## Directory structure

A theme directory has the following structure:

```
theme-name/
├── config.default.yml
├── README.md
└── theme
    ├── css
    │   ├── default.css
    │   └── menu.css
    ├── fonts
    ├── html
    │   ├── home.htm
    │   ├── parts
    │   │   ├── head.htm
    │   │   └── menu.htm
    │   └── simple.htm
    ├── imgs
    ├── js
    └── theme.yml
```

- `css` contains all your CSS files
- `js` contains all your JS files
- `fonts` contains all your fonts
- `imgs` contains all your images
- `html` contains the HTML templates
- `theme.yml` contains the translations, the supported languages and a description of the theme options

You can reference your assets (css, js, imgs, fonts) in the HTML templates using `{{root}}/assets/css|js|fonts|imgs/file`. `{{root}}` being the `rootPath` passed by the user of the theme in the `config.yml`. 

## Content of the html directory

The `html` directory contains at least one directory per supported language and an `index.htm` file in each directory.

Alex uses the [nunjucks template engine](https://mozilla.github.io/nunjucks/) to generate HTML content.

Any file in `html` can be referenced by the templates and be used as partial templates.

Alex passes the following object to each template:

```js
{
    menu: [], // Information about the menu and its sub-menus
    subpages: [], // Sub-menus for the current menu item 
    content: {
        metadata: {},
        content: 'CONTENT FROM content.md',
        [key]: 'CONTENT FROM key.md (key can be replaced by anything)'
    },
    lang: 'THE CURRENT LANGUAGE',
    languages: ['en', 'fr'], // The list of all languages defined in the config.yaml,
    meta: {
        keywords: 'Comma separated keywords',
        description: 'Meta description in the current language',
        [key]: 'Any other meta'
    },
    scripts: [], // List of scripts as in config.yml
    theme: {}, // Information specific to the selected theme
    themeConfig: {}, // Theme configuration (notably passing the translations)
    root: '', // rootPath field in config.yml
}
```

## Tutorial: Designing a simple theme:

### Start by creating the default structure:

```
simple/
├── config.default.yml
├── README.md
└── theme
    ├── css
    │   └── main.css
    ├── fonts
    ├── html
    │   └── index.htm
    ├── imgs
    ├── js
    └── theme.yml
```

### Create the content of `index.htm`:

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width">
        <meta name="description" content="{{meta.description}}"></meta>
        <meta name="keywords" content="{{meta.keywords}}"></meta>
        <link rel="stylesheet" href="{{root}}/assets/css/main.css" type="text/css" media="all">
        <title>{{theme.title}}</title>
        {% for script in scripts %}
            {% if script.content %}
        <script type="text/javascript">
            {{script.content | safe}}
        </script>
            {% else %}
        <script type="text/javascript" src="{{script.source}}"></script>
            {% endif %}
        {% endfor %}
    </head>
    <body>
        <h1 class="h1">{{themeConfig.languages[lang].mainSection}}</h1>

        <section>
            {{content.content | safe}}
        </section>
    </body>
</html>
```

The [safe filter](https://mozilla.github.io/nunjucks/templating.html#safe) is used to tell Nunjucks to not escape the HTML generated from the markdown file. You can add the whole power of Nunjucks to generate complex templates and HTML files. You are limited by your imagination. 

- Add the relevant CSS in main.css and the relevant JS files

### Create the `theme.yml`:

```yaml
supportedLanguages: ['en']
languages: 
    en:
        mainSection: 'My main section'
themeOptions:
    title: 'Website title'
```

The `theme.yml` contains three parts:

- `supportedLanguages`: a list of supported languages. We are using [ISO-639-1 standard](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) (two letters to describe the language such as `fr` for French or `en` for English).
- `languages`: an object describing the translations for each supported languages
- `themeOptions`: an object containing all the options for this theme with a description.

### Create the `config.default.yml`:

```yaml
theme:
    title: "Website's title"
```

It is here to help the user know what kind of options he can use with your theme.

## Advanced topics

### Including partial templates

Often it is useful to break the HTML templates into reusable chunks. You can use the [include](https://mozilla.github.io/nunjucks/templating.html#include) directive from Nunjucks. For instance, if you wan to have a partial template for the HTML head, you can do it like this:

```
simple/
├── config.default.yml
├── README.md
└── theme
    ├── css
    │   └── main.css
    ├── fonts
    ├── html
    │   ├── parts
    │   │   └── head.htm
    │   └── index.htm
    ├── imgs
    ├── js
    └── theme.yml
```

`head.htm`:

```html
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width">
    <meta name="description" content="{{meta.description}}"></meta>
    <meta name="keywords" content="{{meta.keywords}}"></meta>
    <link rel="stylesheet" href="{{root}}/assets/css/main.css" type="text/css" media="all">
    <title>{{theme.title}}</title>
    {% for script in scripts %}
        {% if script.content %}
    <script type="text/javascript">
        {{script.content | safe}}
    </script>
        {% else %}
    <script type="text/javascript" src="{{script.source}}"></script>
        {% endif %}
    {% endfor %}
</head>
```

And then, `index.htm` becomes:

```html
<!DOCTYPE html>
<html>
    {% include "../head.htm" %}
    <body>
        <h1 class="h1">{{themeConfig.languages[lang].mainSection}}</h1>

        <section>
            {{content.content | safe}}
        </section>
    </body>
</html>
```

### Using pages & subpages and referencing them

When Alex generates your website, it outputs a structure like this:

```
my-site
├── assets
│   ├── css
│   │   ├── default.css
│   │   ├── menu.css
│   │   └── monokai.css
│   ├── fonts
│   ├── imgs
│   ├── js
│   └── user
│       ├── files
│       └── imgs
├── en
│   ├── home
│   │   ├── index.htm
│   │   └── submenu1 
│   │       ├── index.htm
│   │       └── sub-submenu1 
│   │           └── index.htm
│   └── index.htm
├── fr
│   ├── home
│   │   ├── index.htm
│   │   └── submenu1 
│   │       ├── index.htm
│   │       └── sub-submenu1 
│   │           └── index.htm
│   └── index.htm
└── index.htm
```

As you can see, each HTML file is called `index.htm` and respects the structure of the `content` directory.

So you can reference the pages and the subpages using that structure. The direct subpages are also passed to the nunjucks template using the `subpages` array. Checkout [Alex! Press](./apress/README.md) that uses this feature for instance.

### Referencing the default page for each language

Alex copies the default page for each language as `index.htm` under each language directory:

```
my-site
├── en
│   └── index.htm
├── fr
│   └── index.htm
└── index.htm
```

So, you can use the `languages` object to iterate over all the available languages for the user and add a link to `/{{language}}`, for instance `/en` or `/fr`. 

### Working with multiple markdown fragments

You are free to use as many markdown fragments as you want for a single page.

Alex passes all markdown fragments to the nunjucks template using the `content` object:

```js
{
    content: {
        metadata: {}, // Merging of metadata from all markdown files 
        content: 'HTML from content.md',
        description: 'HTML from description.md',
        title: 'HTML from title.md',
    }
}
```

This one, you can design complex themes and allow your users to split their markdown files into chunks. Check out [astral](./astral/README.md) that uses this feature for instance.
