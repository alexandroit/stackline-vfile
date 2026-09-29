# @stackline/vfile

> Virtual file format for text processing.

[![npm version](https://img.shields.io/npm/v/@stackline/vfile.svg?style=flat-square)](https://www.npmjs.com/package/@stackline/vfile)
[![license](https://img.shields.io/npm/l/@stackline/vfile.svg?style=flat-square)](https://github.com/alexandroit/stackline-vfile)
[![GitHub repository](https://img.shields.io/badge/GitHub-alexandroit%2Fstackline-vfile-181717?style=flat-square&logo=github)](https://github.com/alexandroit/stackline-vfile)
[![Docs](https://img.shields.io/badge/docs-alexandro.net-0f766e?style=flat-square)](https://alexandro.net/docs/vanilla/vfile/)
[![Reddit community](https://img.shields.io/badge/community-r%2FStackline-ff4500?style=flat-square&logo=reddit&logoColor=white)](https://www.reddit.com/r/Stackline/)

**[Documentation](https://alexandro.net/docs/vanilla/vfile/)** | **[npm](https://www.npmjs.com/package/@stackline/vfile)** | **[Issues](https://github.com/alexandroit/stackline-vfile/issues)** | **[Repository](https://github.com/alexandroit/stackline-vfile)**

**Current package version:** `1.0.1`

---

## Why this package?

`@stackline/vfile` is the Stackline-maintained distribution of `vfile@5.3.7`. It is an independent continuation of [vfile](https://github.com/vfile/vfile); original authors and licenses remain credited below.

## Compatibility

| Item | Value |
| :--- | :--- |
| Package | `@stackline/vfile@1.0.1` |
| API target | `vfile@5.3.7` |
| Supported Node.js | `See supported framework requirements` |
| License | `MIT` |
| Module type | `module` |
| Main entry | `index.js` |
| Types | `index.d.ts` |
| Runtime dependencies | `is-buffer, @types/unist, vfile-message, unist-util-stringify-position` |

## Installation

```bash
npm install @stackline/vfile
```

Preserve existing imports and plugin resolution with an npm alias:

```bash
npm install vfile@npm:@stackline/vfile
```

## Usage and API reference

<h1>
  <img src="https://raw.githubusercontent.com/vfile/vfile/fc8164b/logo.svg?sanitize=true" alt="@stackline/vfile" />
</h1>


**vfile** is a small and browser friendly virtual file format that tracks
metadata about files (such as its `path` and `value`) and lint [messages][].

## Contents

*   [unified](#unified)
*   [What is this?](#what-is-this)
*   [When should I use this?](#when-should-i-use-this)
*   [Install](#install)
*   [Use](#use)
*   [API](#api)
    *   [`VFile(options?)`](#vfileoptions)
    *   [`file.value`](#filevalue)
    *   [`file.cwd`](#filecwd)
    *   [`file.path`](#filepath)
    *   [`file.dirname`](#filedirname)
    *   [`file.basename`](#filebasename)
    *   [`file.extname`](#fileextname)
    *   [`file.stem`](#filestem)
    *   [`file.history`](#filehistory)
    *   [`file.messages`](#filemessages)
    *   [`file.data`](#filedata)
    *   [`VFile#toString(encoding?)`](#vfiletostringencoding)
    *   [`VFile#message(reason[, position][, origin])`](#vfilemessagereason-position-origin)
    *   [`VFile#info(reason[, position][, origin])`](#vfileinforeason-position-origin)
    *   [`VFile#fail(reason[, position][, origin])`](#vfilefailreason-position-origin)
    *   [`BufferEncoding`](#bufferencoding)
    *   [`Compatible`](#compatible)
    *   [`Data`](#data)
    *   [`DataMap`](#datamap)
    *   [`Map`](#map)
    *   [`Options`](#options)
    *   [`Reporter`](#reporter)
    *   [`ReporterSettings`](#reportersettings)
    *   [`Value`](#value)
    *   [Well-known](#well-known)
*   [List of utilities](#list-of-utilities)
*   [Reporters](#reporters)
*   [Types](#types)
*   [Compatibility](#compatibility)
*   [Contribute](#contribute)
*   [Sponsor](#sponsor)
*   [Acknowledgments](#acknowledgments)
*   [License](#license)

## unified

**vfile** is part of the unified collective.

*   for more about us, see [`unifiedjs.com`][site]
*   for how the collective is governed, see [`unifiedjs/collective`][governance]
*   for updates, see [@unifiedjs][twitter] on Twitter

## What is this?

This package provides a virtual file format.
It exposes an API to access the file value, path, metadata about the file, and
specifically supports attaching lint messages and errors to certain places in
these files.

## When should I use this?

The virtual file format is useful when dealing with the concept of files in
places where you might not be able to access the file system.
The message API is particularly useful when making things that check files (as
in, linting).

vfile is made for [unified][], which amongst other things checks files.
However, vfile can be used in other projects that deal with parsing,
transforming, and serializing data, to build linters, compilers, static site
generators, and other build tools.

This is different from the excellent [`vinyl`][vinyl] in that vfile has a
smaller API, a smaller size, and focuses on messages.

## Install

This package is [ESM only][esm].
In Node.js (version 14.14 and 16.0+), install with [npm][]:

```sh
npm install @stackline/vfile
```

In Deno with [`esm.sh`][esmsh]:

```js
import {VFile} from 'https://esm.sh/vfile@5'
```

In browsers with [`esm.sh`][esmsh]:

```html
<script type="module">
  import {VFile} from 'https://esm.sh/vfile@5?bundle'
</script>
```

## Use

```js
import {VFile} from '@stackline/vfile'

const file = new VFile({
  path: '~/example.txt',
  value: 'Alpha *braavo* charlie.'
})

console.log(file.path) // => '~/example.txt'
console.log(file.dirname) // => '~'

file.extname = '.md'

console.log(file.basename) // => 'example.md'

file.basename = 'index.text'

console.log(file.history) // => ['~/example.txt', '~/example.md', '~/index.text']

file.message('Unexpected unknown word `braavo`, did you mean `bravo`?', {
  line: 1,
  column: 8
})

console.log(file.messages)
```

Yields:

```txt
[
  [~/index.text:1:8: Unexpected unknown word `braavo`, did you mean `bravo`?] {
    reason: 'Unexpected unknown word `braavo`, did you mean `bravo`?',
    line: 1,
    column: 8,
    source: null,
    ruleId: null,
    position: {start: [Object], end: [Object]},
    file: '~/index.text',
    fatal: false
  }
]
```

## API

This package exports the identifier [`VFile`][api-vfile].
There is no default export.

### `VFile(options?)`

Create a new virtual file.

`options` is treated as:

*   `string` or [`Buffer`][buffer] — `{value: options}`
*   `URL` — `{path: options}`
*   `VFile` — shallow copies its data over to the new file
*   `object` — all fields are shallow copied over to the new file

Path related fields are set in the following order (least specific to
most specific): `history`, `path`, `basename`, `stem`, `extname`,
`dirname`.

You cannot set `dirname` or `extname` without setting either `history`,
`path`, `basename`, or `stem` too.

###### Parameters

*   `options` ([`Compatible`][api-compatible], optional)
    — file value

###### Returns

New instance (`VFile`).

###### Example

```js
new VFile()
new VFile('console.log("alpha");')
new VFile(Buffer.from('exit 1'))
new VFile({path: path.join('path', 'to', 'readme.md')})
new VFile({stem: 'readme', extname: '.md', dirname: path.join('path', 'to')})
new VFile({other: 'properties', are: 'copied', ov: {e: 'r'}})
```

### `file.value`

Raw value ([`Buffer`][buffer], `string`, `null`).

### `file.cwd`

Base of `path` (`string`, default: `process.cwd()` or `'/'` in browsers).

### `file.path`

Get or set the full path (`string?`, example: `'~/index.min.js'`).

Cannot be nullified.
You can set a file URL (a `URL` object with a `file:` protocol) which will be
turned into a path with [`url.fileURLToPath`][file-url-to-path].

### `file.dirname`

Get or set the parent path (`string?`, example: `'~'`).

Cannot be set if there’s no `path` yet.

### `file.basename`

Get or set the basename (including extname) (`string?`, example: `'index.min.js'`).

Cannot contain path separators (`'/'` on unix, macOS, and browsers, `'\'` on
windows).
Cannot be nullified (use `file.path = file.dirname` instead).

### `file.extname`

Get or set the extname (including dot) (`string?`, example: `'.js'`).

Cannot contain path separators (`'/'` on unix, macOS, and browsers, `'\'` on
windows).
Cannot be set if there’s no `path` yet.

### `file.stem`

Get or set the stem (basename w/o extname) (`string?`, example: `'index.min'`).

Cannot contain path separators (`'/'` on unix, macOS, and browsers, `'\'` on
windows).
Cannot be nullified.

### `file.history`

List of filepaths the file moved between (`Array<string>`).

The first is the original path and the last is the current path.

### `file.messages`

List of messages associated with the file ([`Array<VFileMessage>`][message]).

### `file.data`

Place to store custom information (`Record<string, unknown>`, default: `{}`).

It’s OK to store custom data directly on the file but moving it to `data` is
recommended.

### `VFile#toString(encoding?)`

Serialize the file.

###### Parameters

*   `encoding` ([`BufferEncoding`][api-buffer-encoding], default: `'utf8'`)
    — character encoding to understand `value` as when it’s a
    [`Buffer`][buffer]

###### Returns

Serialized file (`string`).

### `VFile#message(reason[, position][, origin])`

Create a warning message associated with the file.

Its `fatal` is set to `false` and `file` is set to the current file path.
Its added to `file.messages`.

###### Parameters

*   `reason` (`string` or `Error`)
    — reason for message, uses the stack and message of the error if given
*   `place` (`Node`, `Position`, or `Point`, optional)
    — place in file where the message occurred
*   `origin` (`string?`, optional, example: `'my-package:my-rule'` or `'my-rule'`)
    — place in code where the message originates

###### Returns

Message ([`VFileMessage`][vmessage]).

### `VFile#info(reason[, position][, origin])`

Create an info message associated with the file.

Its `fatal` is set to `null` and `file` is set to the current file path.
Its added to `file.messages`.

###### Parameters

*   `reason` (`string` or `Error`)
    — reason for message, uses the stack and message of the error if given
*   `place` (`Node`, `Position`, or `Point`, optional)
    — place in file where the message occurred
*   `origin` (`string?`, optional, example: `'my-package:my-rule'` or `'my-rule'`)
    — place in code where the message originates

###### Returns

Message ([`VFileMessage`][vmessage]).

### `VFile#fail(reason[, position][, origin])`

Create a fatal error associated with the file.

Its `fatal` is set to `true` and `file` is set to the current file path.
Its added to `file.messages`.

> 👉 **Note**: a fatal error means that a file is no longer processable.

###### Parameters

*   `reason` (`string` or `Error`)
    — reason for message, uses the stack and message of the error if given
*   `place` (`Node`, `Position`, or `Point`, optional)
    — place in file where the message occurred
*   `origin` (`string?`, optional, example: `'my-package:my-rule'` or `'my-rule'`)
    — place in code where the message originates

###### Returns

Nothing (`never`).

###### Throws

Message ([`VFileMessage`][vmessage]).

### `BufferEncoding`

[Encodings][encoding] supported by the [buffer][] class (TypeScript type).

This is a copy of the types from Node.

###### Type

```ts
type BufferEncoding =
  | 'ascii'
  | 'utf8'
  | 'utf-8'
  | 'utf16le'
  | 'ucs2'
  | 'ucs-2'
  | 'base64'
  | 'base64url'
  | 'latin1'
  | 'binary'
  | 'hex'
```

### `Compatible`

Things that can be passed to the constructor (TypeScript type).

###### Type

```ts
type Compatible = Options | URL | Value | VFile
```

### `Data`

Custom information (TypeScript type).

Known attributes can be added to [`DataMap`][api-data-map].

###### Type

```ts
type Data = Record<string, unknown> & Partial<DataMap>
```

### `DataMap`

This map registers the type of the `data` key of a `VFile` (TypeScript type).

This type can be augmented to register custom `data` types.

###### Type

```ts
interface DataMap {}
```

###### Example

```ts
declare module '@stackline/vfile' {
  interface DataMap {
    // `file.data.name` is typed as `string`
    name: string
  }
}
```

### `Map`

Raw source map (TypeScript type).

See [`source-map`][source-map].

###### Fields

*   `version` (`number`)
    — which version of the source map spec this map is following
*   `sources` (`Array<string>`)
    — an array of URLs to the original source files
*   `names` (`Array<string>`)
    — an array of identifiers which can be referenced by individual mappings
*   `sourceRoot` (`string`, optional)
    — the URL root from which all sources are relative
*   `sourcesContent` (`Array<string>`, optional)
    — an array of contents of the original source files
*   `mappings` (`string`)
    — a string of base64 VLQs which contain the actual mappings
*   `file` (`string`)
    — the generated file this source map is associated with

### `Options`

An object with arbitrary fields and the following known fields (TypeScript
type).

###### Fields

*   `value` ([`Value`][api-value], optional)
    — set `value`
*   `cwd` (`string`, optional)
    — set `cwd`
*   `history` (`Array<string>`, optional)
    — set `history`
*   `path` (`URL | string`, optional)
    — set `path`
*   `basename` (`string`, optional)
    — set `basename`
*   `stem` (`string`, optional)
    — set `stem`
*   `extname` (`string`, optional)
    — set `extname`
*   `dirname` (`string`, optional)
    — set `dirname`
*   `data` ([`Data`][api-data], optional)
    — set `data`

### `Reporter`

Type for a reporter (TypeScript type).

###### Type

```ts
type Reporter<Settings extends ReporterSettings> = (
  files: Array<VFile>,
  options: Settings
) => string
```

### `ReporterSettings`

Configuration for reporters (TypeScript type).

###### Type

```ts
type ReporterSettings = Record<string, unknown>
```

### `Value`

Contents of the file (TypeScript type).

Can either be text or a `Buffer` structure.

###### Type

```ts
type Value = string | Buffer
```

### Well-known

The following fields are considered “non-standard”, but they are allowed, and
some utilities use them:

*   `stored` (`boolean`)
    — whether a file was saved to disk; this is used by vfile reporters
*   `result` (`unknown`)
    — custom, non-string, compiled, representation; this is used by unified to
    store non-string results; one example is when turning markdown into React
    nodes
*   `map` ([`Map`][api-map])
    — source map; this type is equivalent to the `RawSourceMap` type from the
    `source-map` module

There are also well-known fields on messages, see
[them in a similar section of
`vfile-message`](https://github.com/vfile/vfile-message#well-known).

<a name="utilities"></a>

## List of utilities

*   [`convert-vinyl-to-vfile`](https://github.com/dustinspecker/convert-vinyl-to-vfile)
    — transform from [Vinyl][]
*   [`to-vfile`](https://github.com/vfile/to-vfile)
    — create a file from a filepath and read and write to the file system
*   [`vfile-find-down`](https://github.com/vfile/vfile-find-down)
    — find files by searching the file system downwards
*   [`vfile-find-up`](https://github.com/vfile/vfile-find-up)
    — find files by searching the file system upwards
*   [`vfile-glob`](https://github.com/shinnn/vfile-glob)
    — find files by glob patterns
*   [`vfile-is`](https://github.com/vfile/vfile-is)
    — check if a file passes a test
*   [`vfile-location`](https://github.com/vfile/vfile-location)
    — convert between positional and offset locations
*   [`vfile-matter`](https://github.com/vfile/vfile-matter)
    — parse the YAML front matter
*   [`vfile-message`](https://github.com/vfile/vfile-message)
    — create a file message
*   [`vfile-messages-to-vscode-diagnostics`](https://github.com/shinnn/vfile-messages-to-vscode-diagnostics)
    — transform file messages to VS Code diagnostics
*   [`vfile-mkdirp`](https://github.com/vfile/vfile-mkdirp)
    — make sure the directory of a file exists on the file system
*   [`vfile-rename`](https://github.com/vfile/vfile-rename)
    — rename the path parts of a file
*   [`vfile-sort`](https://github.com/vfile/vfile-sort)
    — sort messages by line/column
*   [`vfile-statistics`](https://github.com/vfile/vfile-statistics)
    — count messages per category: failures, warnings, etc
*   [`vfile-to-eslint`](https://github.com/vfile/vfile-to-eslint)
    — convert to ESLint formatter compatible output

> 👉 **Note**: see [unist][] for projects that work with nodes.

## Reporters

*   [`vfile-reporter`][reporter]
    — create a report
*   [`vfile-reporter-json`](https://github.com/vfile/vfile-reporter-json)
    — create a JSON report
*   [`vfile-reporter-folder-json`](https://github.com/vfile/vfile-reporter-folder-json)
    — create a JSON representation of vfiles
*   [`vfile-reporter-pretty`](https://github.com/vfile/vfile-reporter-pretty)
    — create a pretty report
*   [`vfile-reporter-junit`](https://github.com/kellyselden/vfile-reporter-junit)
    — create a jUnit report
*   [`vfile-reporter-position`](https://github.com/Hocdoc/vfile-reporter-position)
    — create a report with content excerpts

> 👉 **Note**: want to make your own reporter?
> Reporters *must* accept `Array<VFile>` as their first argument, and return
> `string`.
> Reporters *may* accept other values too, in which case it’s suggested to stick
> to `vfile-reporter`s interface.

## Types

This package is fully typed with [TypeScript][].
It exports the additional types
[`BufferEncoding`][api-buffer-encoding],
[`Compatible`][api-compatible],
[`Data`][api-data],
[`DataMap`][api-data-map],
[`Map`][api-map],
[`Options`][api-options],
[`Reporter`][api-reporter],
[`ReporterSettings`][api-reporter-settings], and
[`Value`][api-value].

## Compatibility

Projects maintained by the unified collective are compatible with all maintained
versions of Node.js.
As of now, that is Node.js 14.14+ and 16.0+.
Our projects sometimes work with older versions, but this is not guaranteed.

## Contribute

See [`contributing.md`][contributing] in [`vfile/.github`][health] for ways to
get started.
See [`support.md`][support] for ways to get help.

This project has a [code of conduct][coc].
By interacting with this repository, organization, or community you agree to
abide by its terms.

## Sponsor

Support this effort and give back by sponsoring on [OpenCollective][collective]!

<table>
<tr valign="middle">
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://vercel.com">Vercel</a><br><br>
  <a href="https://vercel.com"><img src="https://avatars1.githubusercontent.com/u/14985020?s=256&v=4" width="128"></a>
</td>
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://motif.land">Motif</a><br><br>
  <a href="https://motif.land"><img src="https://avatars1.githubusercontent.com/u/74457950?s=256&v=4" width="128"></a>
</td>
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://www.hashicorp.com">HashiCorp</a><br><br>
  <a href="https://www.hashicorp.com"><img src="https://avatars1.githubusercontent.com/u/761456?s=256&v=4" width="128"></a>
</td>
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://www.gitbook.com">GitBook</a><br><br>
  <a href="https://www.gitbook.com"><img src="https://avatars1.githubusercontent.com/u/7111340?s=256&v=4" width="128"></a>
</td>
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://www.gatsbyjs.org">Gatsby</a><br><br>
  <a href="https://www.gatsbyjs.org"><img src="https://avatars1.githubusercontent.com/u/12551863?s=256&v=4" width="128"></a>
</td>
</tr>
<tr valign="middle">
</tr>
<tr valign="middle">
<td width="20%" align="center" rowspan="2" colspan="2">
  <a href="https://www.netlify.com">Netlify</a><br><br>
  
  <a href="https://www.netlify.com"><img src="https://images.opencollective.com/netlify/4087de2/logo/256.png" width="128"></a>
</td>
<td width="10%" align="center">
  <a href="https://www.coinbase.com">Coinbase</a><br><br>
  <a href="https://www.coinbase.com"><img src="https://avatars1.githubusercontent.com/u/1885080?s=256&v=4" width="64"></a>
</td>
<td width="10%" align="center">
  <a href="https://themeisle.com">ThemeIsle</a><br><br>
  <a href="https://themeisle.com"><img src="https://avatars1.githubusercontent.com/u/58979018?s=128&v=4" width="64"></a>
</td>
<td width="10%" align="center">
  <a href="https://expo.io">Expo</a><br><br>
  <a href="https://expo.io"><img src="https://avatars1.githubusercontent.com/u/12504344?s=128&v=4" width="64"></a>
</td>
<td width="10%" align="center">
  <a href="https://boostnote.io">Boost Note</a><br><br>
  <a href="https://boostnote.io"><img src="https://images.opencollective.com/boosthub/6318083/logo/128.png" width="64"></a>
</td>
<td width="10%" align="center">
  <a href="https://www.holloway.com">Holloway</a><br><br>
  <a href="https://www.holloway.com"><img src="https://avatars1.githubusercontent.com/u/35904294?s=128&v=4" width="64"></a>
</td>
<td width="10%"></td>
<td width="10%"></td>
<td width="10%"></td>
</tr>
<tr valign="middle">
<td width="100%" align="center" colspan="8">
  <br>
  <a href="https://opencollective.com/unified"><strong>You?</strong></a>
  <br><br>
</td>
</tr>
</table>

## Acknowledgments

The initial release of this project was authored by
[**@wooorm**](https://github.com/wooorm).

Thanks to [**@contra**](https://github.com/contra),
[**@phated**](https://github.com/phated), and others for their work on
[Vinyl][], which was a huge inspiration.

Thanks to
[**@brendo**](https://github.com/brendo),
[**@shinnn**](https://github.com/shinnn),
[**@KyleAMathews**](https://github.com/KyleAMathews),
[**@sindresorhus**](https://github.com/sindresorhus), and
[**@denysdovhan**](https://github.com/denysdovhan)
for contributing commits since!

## License

[MIT][license] © [Titus Wormer][author]



[build-badge]: https://github.com/vfile/vfile/workflows/main/badge.svg

[build]: https://github.com/vfile/vfile/actions

[coverage-badge]: https://img.shields.io/codecov/c/github/vfile/vfile.svg

[coverage]: https://codecov.io/github/vfile/vfile

[downloads-badge]: https://img.shields.io/npm/dm/vfile.svg

[downloads]: https://www.npmjs.com/package/vfile

[size-badge]: https://img.shields.io/bundlephobia/minzip/vfile.svg

[size]: https://bundlephobia.com/result?p=vfile

[sponsors-badge]: https://opencollective.com/unified/sponsors/badge.svg

[backers-badge]: https://opencollective.com/unified/backers/badge.svg

[collective]: https://opencollective.com/unified

[chat-badge]: https://img.shields.io/badge/chat-discussions-success.svg

[chat]: https://github.com/vfile/vfile/discussions

[npm]: https://docs.npmjs.com/cli/install

[esm]: https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c

[esmsh]: https://esm.sh

[typescript]: https://www.typescriptlang.org

[health]: https://github.com/vfile/.github

[contributing]: https://github.com/vfile/.github/blob/main/contributing.md

[support]: https://github.com/vfile/.github/blob/main/support.md

[coc]: https://github.com/vfile/.github/blob/main/code-of-conduct.md

[license]: license

[author]: https://wooorm.com

[unified]: https://github.com/unifiedjs/unified

[vinyl]: https://github.com/gulpjs/vinyl

[site]: https://unifiedjs.com

[twitter]: https://twitter.com/unifiedjs

[unist]: https://github.com/syntax-tree/unist#list-of-utilities

[reporter]: https://github.com/vfile/vfile-reporter

[vmessage]: https://github.com/vfile/vfile-message

[messages]: #filemessages

[message]: #vfilemessagereason-position-origin

[encoding]: https://nodejs.org/api/buffer.html#buffer_buffers_and_character_encodings

[buffer]: https://nodejs.org/api/buffer.html

[source-map]: https://github.com/mozilla/source-map/blob/58819f0/source-map.d.ts#L15-L23

[file-url-to-path]: https://nodejs.org/api/url.html#url_url_fileurltopath_url

[governance]: https://github.com/unifiedjs/collective

[api-vfile]: #vfileoptions

[api-buffer-encoding]: #bufferencoding

[api-compatible]: #compatible

[api-data]: #data

[api-data-map]: #datamap

[api-map]: #map

[api-options]: #options

[api-reporter]: #reporter

[api-reporter-settings]: #reportersettings

[api-value]: #value

## Credits and original authors

- Original project: [vfile](https://github.com/vfile/vfile).
- Titus Wormer.
- Brendan Abbott.
- Denys Dovhan.
- Kyle Mathews.
- Shinnosuke Watanabe.
- Sindre Sorhus.
- Copyright (c) 2015 Titus Wormer <tituswormer@gmail.com>.
- Stackline maintenance: [Alexandro Paixao Marques](https://www.linkedin.com/in/aleinfo/) and [Stackline contributors](https://github.com/alexandroit).

Original copyright, license notices and contributor acknowledgements remain part of this distribution. Stackline maintenance does not replace authorship of the original work.

## Community and Links

- [Stackline website](https://alexandro.net/)
- [GitHub projects](https://github.com/alexandroit)
- [npm packages](https://www.npmjs.com/~alex360qc)
- [Reddit community — r/Stackline](https://www.reddit.com/r/Stackline/)
- [Maintainer LinkedIn](https://www.linkedin.com/in/aleinfo/)

Use this repository's issue tracker for reproducible bugs and feature requests. Join r/Stackline for examples, usage questions and release discussions.
