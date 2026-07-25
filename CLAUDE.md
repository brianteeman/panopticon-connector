# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Akeeba Panopticon Connector is a **Joomla 4/5 component** (PHP) that exposes REST API endpoints for [Akeeba Panopticon](https://github.com/akeeba/panopticon), a self-hosted site monitoring tool. It allows Panopticon to remotely monitor updates, install extensions, verify core file checksums, and interact with Akeeba Backup and Admin Tools on connected Joomla sites.

**License:** AGPL-3.0+

## Build Commands

Builds use **Apache Phing** and **require the sibling `../buildfiles` repo** to be checked out next to this one (`../buildfiles/phing/common.xml`). See the Phing Build skill for the target reference.

## Tests

Two PHPUnit suites — see `tests/README.md` for the full picture:

- **Unit** (`phpunit -c phpunit.xml`) — pure logic; no Joomla, no database, no network.
- **Integration** (`tests/integration/run-tests.sh [4|5|6]`) — drives the real REST API over HTTP against a Dockerised Joomla install. Needs Docker and the sibling `../buildfiles` repo.

PHPUnit is deliberately **not** in `composer.json`: `vendor-dir` points at the shipped `component/backend/vendor`, so a `require-dev` would leak PHPUnit into the release package. Use a PHPUnit 10/11 on your `PATH` or a `phpunit-11.phar`.

No linter or CI pipeline is configured.

## Architecture

### Joomla Component (MVC)

`component/api/` is the REST API layer and the point of this project. `component/backend/` is a near-empty Joomla admin shell (options/configuration only) that also hosts the vendored Composer dependencies.

### Plugins

- **webservices** — the single route definition file: all API endpoints are declared in `src/Extension/Panopticon.php`.
- **system** — error handling for API requests.
- **console** — CLI command for generating API tokens.

### API Route Pattern

All routes are prefixed with `v1/panopticon/` and map to controller actions. The route → controller mapping is entirely defined in the WebServices plugin. Controllers handle authorization via Super User checks and return JSON:API formatted responses.

### Key Integration Points

The connector optionally integrates with:
- **Akeeba Backup Professional** — backup status and version info
- **Admin Tools Professional** — IP unblocking, .htaccess management, file change scanning, temporary super users

These integrations are detected at runtime; the connector works without them.

## Coding Conventions

- **File header:** Every PHP file starts with the standard `@package panopticon` / copyright / license block, followed by `defined('_JEXEC') || die;`
- **Indentation:** Tabs, not spaces
- **Authorization:** Controllers check for Super User access; unauthorized requests throw `NotAllowed` exceptions
- **Version tokens:** Build templates use `##VERSION##` and `##DATE##` placeholders replaced by Phing

## Versioning

- Version, date, and API level are maintained in `component/backend/version.php`
- The API level (`AKEEBA_PANOPTICON_API`) is an integer that tracks breaking API changes
- Package manifests (`pkg_panopticon.xml`, `component/panopticon.xml`) also contain version strings updated at build time
