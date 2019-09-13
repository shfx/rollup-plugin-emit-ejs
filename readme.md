# rollup-plugin-emit-ejs

This plugin allows you to emit files from ejs templates to rollup bundle.

It is primarily meant to emit html files since it helps you to link bundled javascripts/stylesheets and includes a basic layout system, but it can deal with any file type.

Unlike [rollup-plugin-bundle-html](https://github.com/haifeng2013/rollup-plugin-bundle-html), this plugin uses the [emitFile()](https://rollupjs.org/guide/en/#thisemitfileemittedfile-emittedchunk--emittedasset--string) plugin context method which allow other plugins like [html-minifier](https://github.com/juliendargelos/rollup-plugin-html-minifier) to process the emitted files.

## Install

```bash
yarn add rollup-plugin-emit-ejs --dev
```

or

```bash
npm install rollup-plugin-emit-ejs -D
```

## Usage

```javascript
// rollup.config.js

import emitEJS from 'rollup-plugin-emit-ejs'

export default {
  // ...
  plugins: [
    emitEJS({
      src: 'src',
      layout: 'src/layout.html.ejs',
      data: {
        title: 'Hey',
        body: 'Hey Hey Hey'
      }
    })
  ]
}
```

```ejs
<!-- src/layout.html.ejs !-->

<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title><%= title %></title>
    <%- stylesheets %>
  </head>
  <body>
    <%- content %>
    <%- javascripts %>
  </body>
</html>
```

```ejs
<!-- src/index.html.ejs !-->

<main>
  <p><%= body %></p>
</main>
```

This will emit a file named `index.html` next to the javascript bundle file.

## Options

```typescript
{
  src: string
  dest?: string
  include?: string | string[]
  exclude?: string | string[]
  extension?: string
  layout?: string
  javascript?: (file: string) => string
  stylesheet?: (file: string) => string
  data?: Data
  options?: Options
}
```

### src

Source directory to find ejs templates from.

Required

### dest

Directory where to output files from ejs templates.

Default: either `output.file`, `output.dir` or `undefined` depending on your rollup configuration.

> The destination directory is automatically inferred from your [rollup output options](https://rollupjs.org/guide/en/#outputdir) so it is the same of your bundle files. You can set it explicitly if none of `output.file`  and `output.dir` options are provided.
> If the destination is neither provided by `dest`, `output.file` nor `output.dir` option, the plugin will throw an error.

### include

Glob or array of globs defining which templates to include.

Default: `'**/*.ejs'`
  
### exclude

Glob or array of globs defining which templates to exclude.

Default: `[]`

> Note that the template provided in the `layout` option is automatically excluded.

### extension

Extension to append to emitted files (the leading `.` can be omitted).

Default: `undefined`

> Note that the trailing `.ejs` extension is automatically removed from template filenames. So this option is useful if you only want to use the `.ejs` extension in your template filenames, but need to replace it with another extension like `.html`. Otherwise, you can just stack the output extension directly in the filename (`index.html.ejs`) and skip this option.

### layout

Path to an ejs template to use as layout. Skip this option if you don't need layout.

Default: `undefined`

### javascript

Function called for printing script tags linking to javascript bundle files.

Default: <code>file => &grave;&lt;script src="${file}"&gt;&lt;/script&gt;&grave;</code>

### stylesheet

Function called for printing link tags linking to css bundle files.

Default: <code>file => &grave;&lt;link rel="stylesheet" href="${file}"&gt;&grave;</code>

### data

Data to pass to ejs.

Default: `{}`

> The following helper variables are forced by the plugin and available in all templates:
> - `javascripts`: script tags linking to javascript bundles
> - `stylesheets`: link tags linking to css bundles
>
> In the layout, an extra `content` variable is passed containing the content to wrap into the layout.
>
> These variables need to be printed **unescaped** if you wish to use it as html, use the corresponding ejs tag: `<%-` (See [ejs tags](https://github.com/mde/ejs#tags))

### options

Options to pass to ejs. (See [ejs options](https://github.com/mde/ejs#options))

Default: `{}`

> Note that the `filename` options is forced by the plugin.
