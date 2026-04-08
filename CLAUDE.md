# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

### Setup
```bash
flutter pub get
flutter pub get -C flutter_quill_extensions
flutter pub get -C flutter_quill_test
```

### Testing
```bash
flutter test                                                          # all tests in root package
flutter test -C flutter_quill_extensions
flutter test -C flutter_quill_test
flutter test test/controller/quill_controller_test.dart              # single test file
```

### Analysis & fixes
```bash
flutter analyze
dart fix --apply
```

### Pre-push checks
```bash
dart ./scripts/before_push.dart
```

### Example app
```bash
cd example && flutter run
```

### Translations
```bash
dart ./scripts/translations_check.dart       # validate format
dart ./scripts/regenerate_translations.dart
```

## Architecture

This is a monorepo with three published packages:

| Package | Purpose |
|---|---|
| `flutter_quill` (root) | Core rich-text editor — document model, editor widget, toolbar |
| `flutter_quill_extensions/` | Optional embeds: images, video, formula, camera |
| `flutter_quill_test/` | Test helpers (`quillEnterText`, `quillReplaceText`, etc.) |
| `example/` | Demo app (not published) |

### Core module layout (`lib/src/`)
- **`controller/`** — `QuillController`: single source of truth for document state and selection
- **`document/`** — Document model, Delta attributes, styles; built on `dart_quill_delta`
- **`editor/`** — `QuillEditor` widget, raw editor, rendering, focus/scroll handling
- **`toolbar/`** — `QuillSimpleToolbar` and individual button widgets
- **`rules/`** — Insert/delete/format rules that transform Deltas (applied by the controller)
- **`delta/`** — Delta diff utilities
- **`editor_toolbar_shared/`** — Config objects shared between editor and toolbar
- **`l10n/`** — Localization (ARB files + generated Dart)
- **`common/`** — Shared utilities and theme helpers

### Document model
Documents are stored as **Quill Delta** — a sequence of `insert`/`retain`/`delete` operations with optional attributes. The `dart_quill_delta` package owns the core Delta type; `flutter_quill` builds the document, node tree, and rendering on top of it. See `doc/delta_introduction.md` and `doc/attribute_introduction.md` for deep dives.

### Embed system
Custom embeds (images, video, etc.) implement `EmbedBuilder` and are passed to `QuillEditor` via `embedBuilders`. `flutter_quill_extensions` provides ready-made builders; apps can supply their own.

## Conventions

- **Single quotes** for strings; **relative imports** within a package.
- **Always declare return types**; prefer `const` constructors and `final` fields.
- **CHANGELOG.md** — add changes under the `## [Unreleased]` section (Keep a Changelog format). Do not bump version numbers (automated by maintainers).
- Do not modify `pubspec.yaml` version fields.
- Analysis options are strict (`analysis_options.yaml` at root applies to all packages).
