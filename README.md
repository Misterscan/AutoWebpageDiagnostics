<!-- banner -->
<div align="center">
<img src="https://raw.githubusercontent.com/Misterscan/AutoWebDiagnostics/main/assets/banner.svg" alt="banner" width="100%" />

<br/>

<img src="https://raw.githubusercontent.com/Misterscan/AutoWebDiagnostics/main/assets/badge-version.svg" alt="Version" height="20"/>
<img src="https://raw.githubusercontent.com/Misterscan/AutoWebDiagnostics/main/assets/badge-sesi.svg" alt="Sesi" height="20"/>
<img src="https://raw.githubusercontent.com/Misterscan/AutoWebDiagnostics/main/assets/badge-browser.svg" alt="Browser" height="20"/>
<img src="https://raw.githubusercontent.com/Misterscan/AutoWebDiagnostics/main/assets/badge-license.svg" alt="License" height="20"/>

</div>

A Sesi package that runs a comprehensive, autonomous diagnostic suite against any target URL using the built-in `std/browser` library — ready for autonomous AI-Agent debugging.

**Checks:** Performance · SEO · Accessibility · Security · Broken Links · Console Errors

---

## 📦 Install

```bash
sesi install github:misterscan/AutoWebDiagnostics
```

Then import in your script:

```sesi
// Full namespace
allow "AutoWebDiagnostics" in with Diagnostics
let report = Diagnostics.run("https://example.com", "reports")

// Named imports
allow "AutoWebDiagnostics" in with {run, seo}
let report = run("https://example.com", "reports")
```

## 📖 API

### `run(targetUrl, outputDir)` → `object`

Runs all checks in sequence, saves the report, and returns a summary object.

```sesi
allow "AutoWebDiagnostics" in with {run}

let report = run("https://example.com", "reports")
print report["passed"]   // number of checks passed
print report["flagged"]  // number of checks flagged
```

**Returns:**

```
{
  ok, url, title, reportPath,
  totalChecks, passed, flagged, results,
  seo, a11y, security, links, console, resources
}
```

---

### Individual Check Functions

Each check takes an active `page` object and returns its own result slice. Useful when you only need specific diagnostics.

```sesi
allow "std/browser" in with Browser
allow "AutoWebDiagnostics" in with {
  seo, accessibility
}

let browser = Browser.launch({"headless": true})
let page = browser.newPage()
page.goto("https://example.com")

let seo  = seo(page)
let a11y = accessibility(page)

print seo["h1Count"]
print a11y["imgsMissingAlt"]

browser.close()
```

| Function        | Signature             | Returns                                                                 |
| --------------- | --------------------- | ----------------------------------------------------------------------- |
| `performance`   | `(page, url)`         | `{ok, title, loadMs, results}`                                          |
| `seo`           | `(page)`              | `{hasMeta, h1Count, canonical, ogTitle, robotsMeta, hasOg, results}`    |
| `accessibility` | `(page)`              | `{imgsMissingAlt, inputsMissingLabel, langAttr, viewportMeta, results}` |
| `security`      | `(page, url)`         | `{hasHttps, hasCsp, results}`                                           |
| `links`         | `(page)`              | `{brokenLinks, brokenCount, checkedLinks, results}`                     |
| `console`       | `(page)`              | `{jsErrors, results}`                                                   |
| `resources`     | `(page)`              | `{domNodes, scriptCount, styleCount, imgCount, results}`                |
| `save_report`   | `(report, outputDir)` | `reportPath: string`                                                    |

---

### Utilities

```sesi
allow "AutoWebDiagnostics" in with {pass_icon, warn_icon, make_separator}

print pass_icon(true)          // ✅
print warn_icon(false)         // ⚠️
print make_separator("Hello")  // ══ Hello ══
```

---

## 🗂 Structure

```
AutoWebDiagnostics/
├── index.sesi       ← package entry point (re-exports main.sesi)
├── main.sesi        ← all exported diagnostic functions
├── run.sesi         ← standalone CLI runner
├── sesi.json        ← package manifest
├── bin/
│   ├── lint.sesi
│   └── test-runner.sesi
├── helpers/         ← file manipulation utilities
└── reports/         ← generated output (gitignored)
```

---

## Commands

```bash
npm run eval "<sesi code>"      # inline eval
npm run lint                    # lint a file
```
