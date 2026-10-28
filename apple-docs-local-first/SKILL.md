---
name: "apple-docs-local-first"
description: "For any question about Apple platforms, frameworks, Swift or Xcode (SwiftUI, UIKit, iOS, macOS, visionOS, watchOS, HIG, Swift Evolution), look it up in the local cupertino docs server before the web."
---

# Apple docs: local cupertino first

The user runs a local documentation server called **cupertino** (Apple Developer Documentation, HIG, Apple Sample Code, Apple Archive, Swift Evolution, Swift.org, The Swift Book, Swift package docs). It is faster and cheaper than fetching web pages. Always try it first for Apple / Swift questions.

## Steps

1. **Load the tools.** Run ToolSearch with the query `cupertino`. Tool names look like `mcp__claude-device__lcl-cupertino-search` (the prefix can vary by session). Load only the ones you need.
2. **Search with keywords, not sentences.** The index is SQLite FTS5 keyword search. Use 1-5 terms (symbol names, framework names, concept words).
   - Set `limit` to 5 unless you need more.
   - Set `source` when it is obvious: `apple-docs` (API reference), `samples` (working code), `hig` (design guidance), `swift-evolution` (language proposals), `swift-book` (language rules), `apple-archive` (legacy guides such as Core Animation, Quartz 2D, KVO).
   - Use `framework` (e.g. `swiftui`) and `min_ios` / `min_macos` / `min_visionos` filters when the question names a framework or OS version.
   - For code-level questions prefer the AST tools: `search_symbols`, `search_conformances`, `search_concurrency`, `search_property_wrappers`, `search_generics`, `get_inheritance`.
3. **Read narrowly.** If the search snippet already answers the question, answer from it. Otherwise read only the one or two most relevant documents with `read_document` (by URI). For sample code, use `list_samples` / `read_sample` to find the project and `read_sample_file` for the specific file only; never read a whole project.
4. **Answer** from what cupertino returned, naming the document title (and URI when useful).

## When to fall back to the web

- The cupertino tools are not present in this session (desktop app not linked, or the server is off). Say so in one line, then continue.
- Two differently worded searches returned nothing relevant.
- The question needs information the local index cannot have: App Store Review Guidelines or policy, release notes or APIs newer than the index, news, pricing, WWDC session content.

When falling back, go straight to the specific developer.apple.com / swift.org page with WebFetch rather than a broad WebSearch, and fetch as few pages as possible.

## Token hygiene

- Don't page through long result lists; refine the query instead.
- Don't read a full document when a section or snippet answers the question.
- Don't search both cupertino and the web for the same fact unless cupertino's answer looks outdated.