# Nuxt 4.4.2 Windows CSS `@fs` Reproduction

This repository is a minimal reproduction for a Windows-only Nuxt development issue where CSS injected from a package can be emitted with a malformed Vite file-system URL.

Instead of generating a valid path such as:

```text
/@fs/D:/project/.../style.css
```

Nuxt may emit:

```text
/@fsD:/project/.../style.css
```

That malformed URL returns 404 in development.

## Target Environment

- Windows 10 or Windows 11
- Nuxt 4.4.2
- Vite builder
- A package CSS entry resolved through `nuxt.options.css`

The issue is path-shape dependent, so Linux-based online sandboxes may not reproduce it.

## Repro Setup

This project does not use a third-party package to trigger the bug. It includes a local file dependency instead:

- Root dependency: `windows-css-repro`
- Local package path: `packages/windows-css-repro`
- CSS entry added in `nuxt.config.ts`: `windows-css-repro/style.css`

That setup is enough to drive Nuxt through the problematic Windows `@fs` URL generation path.

## Install

```bash
npm install
```

## Reproduction Steps

1. Start the Nuxt dev server:

   ```bash
   npm run dev
   ```

2. Open the local URL printed in the terminal.

3. Open browser DevTools:
   - `F12`, or
   - `Ctrl+Shift+I`

4. Check one of the following:
   - `Console` tab for stylesheet 404 errors
   - `Network` tab for requests containing `@fsD:`
   - `Elements` tab for injected `<link rel="stylesheet">` entries

## Expected Result

You should see a malformed stylesheet request similar to:

```text
/_nuxt/@fsD:/project/demo/nuxt442-issues-34766/packages/windows-css-repro/style.css
```

That request should return `404 Not Found`.

You may also see a valid-looking `@fs/D:/...` path in the page output, but the malformed `@fsD:/...` request is the failure signal this repository is meant to demonstrate.

## Relevant Nuxt Code Path

The issue is in `@nuxt/vite-builder`, inside `getManifest()`.

Current logic:

```js
css.add('/@fs' + resolved)
```

On Windows, when `resolved` is shaped like `D:/path/to/style.css`, this becomes:

```text
/@fsD:/path/to/style.css
```

## Suggested Fix

```js
css.add('/@fs' + (resolved.startsWith('/') ? resolved : '/' + resolved))
```

## Files In This Reproduction

- `nuxt.config.ts`: adds the package CSS entry
- `packages/windows-css-repro/package.json`: local package export definition
- `packages/windows-css-repro/style.css`: minimal CSS payload
- `app/app.vue`: on-page repro instructions
